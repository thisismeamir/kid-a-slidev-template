<script setup lang="ts">
import {
  computed,
  onMounted,
  onUnmounted,
  ref,
  watch,
} from 'vue'

interface Point {
  x: number
  y: number
}

interface GridPoint extends Point {
  gx: number
  gy: number
  gz: number
}

interface CurveNode {
  t: number

  current: Point
  from: Point
  to: Point
}

type CurvePhase = 'appearing' | 'alive' | 'disappearing'

interface Curve {
  start: GridPoint
  end: GridPoint

  nodes: CurveNode[]

  progress: number
  duration: number

  opacity: number
  phase: CurvePhase
}

const props = withDefaults(defineProps<{
  strings?: number

  gridX?: number
  gridY?: number
  gridZ?: number

  width?: number
  height?: number

  spacing?: number
  angle?: number
  depthScale?: number

  minNodes?: number
  maxNodes?: number
  curvature?: number

  strokeWidth?: number
  color?: string
  pointSize?: number

  gridOpacity?: number
  opacity?: number

  minDuration?: number
  maxDuration?: number

  fadeDuration?: number
}>(), {
  strings: 8,

  gridX: 10,
  gridY: 10,
  gridZ: 2,

  width: 100,
  height: 100,

  spacing: 30,
  angle: 30,
  depthScale: 1.0,

  minNodes: 1,
  maxNodes: 3,
  curvature: 0.45,

  strokeWidth: 0.4,
  color: 'var(--orion-60)',
  pointSize: 1.2,

  gridOpacity: 0.15,
  opacity: 1,

  minDuration: 4000,
  maxDuration: 20000,

  fadeDuration: 100,
})

const grid = ref<GridPoint[]>([])
const curves = ref<Curve[]>([])

let animationFrame: number | undefined

const angle = computed(
  () => props.angle * Math.PI / 180,
)

function random(min: number, max: number) {
  return min + Math.random() * (max - min)
}

function pick<T>(array: T[]): T {
  return array[
    Math.floor(Math.random() * array.length)
  ]
}

/* -------------------------------------------------------------------------- */
/* Isometric grid                                                             */
/* -------------------------------------------------------------------------- */

function project(
  x: number,
  y: number,
  z: number,
): Point {
  return {
    x:
      (x - y)
      * Math.cos(angle.value)
      * props.spacing,

    y:
      (x + y)
      * Math.sin(angle.value)
      * props.spacing
      -
      z
      * props.spacing
      * props.depthScale,
  }
}

function buildGrid() {
  const points: GridPoint[] = []

  for (let z = 0; z < props.gridZ; z++) {
    for (let y = 0; y < props.gridY; y++) {
      for (let x = 0; x < props.gridX; x++) {
        const p = project(x, y, z)

        points.push({
          gx: x,
          gy: y,
          gz: z,
          x: p.x,
          y: p.y,
        })
      }
    }
  }

  const minX = Math.min(
    ...points.map(p => p.x),
  )

  const maxX = Math.max(
    ...points.map(p => p.x),
  )

  const minY = Math.min(
    ...points.map(p => p.y),
  )

  const maxY = Math.max(
    ...points.map(p => p.y),
  )

  const ox =
    props.width / 2 -
    (minX + maxX) / 2

  const oy =
    props.height / 2 -
    (minY + maxY) / 2

  for (const p of points) {
    p.x += ox
    p.y += oy
  }

  grid.value = points
}

/* -------------------------------------------------------------------------- */
/* Random curve geometry                                                      */
/* -------------------------------------------------------------------------- */

function makeMovingPoint(
  start: GridPoint,
  end: GridPoint,
  t: number,
): Point {
  const dx = end.x - start.x
  const dy = end.y - start.y

  const length =
    Math.sqrt(dx * dx + dy * dy)

  if (length === 0) {
    return {
      x: start.x,
      y: start.y,
    }
  }

  const ux = dx / length
  const uy = dy / length

  const nx = -uy
  const ny = ux

  /*
   * Two independent random components:
   *
   * 1. movement along the string
   * 2. movement perpendicular to it
   *
   * This means the intermediate points don't all
   * simply "bend" in one common direction.
   */
  const longitudinal =
    random(-length * 0.12, length * 0.12)

  const transverse =
    random(
      -length * props.curvature,
      length * props.curvature,
    )

  return {
    x:
      start.x
      + dx * t
      + ux * longitudinal
      + nx * transverse,

    y:
      start.y
      + dy * t
      + uy * longitudinal
      + ny * transverse,
  }
}

function createNodes(
  start: GridPoint,
  end: GridPoint,
): CurveNode[] {
  const count = Math.floor(
    random(
      props.minNodes,
      props.maxNodes + 1,
    ),
  )

  const nodes: CurveNode[] = []

  for (let i = 0; i < count; i++) {
    const t =
      (i + 1) / (count + 1)

    const point =
      makeMovingPoint(start, end, t)

    nodes.push({
      t,

      current: { ...point },
      from: { ...point },

      to:
        makeMovingPoint(
          start,
          end,
          t,
        ),
    })
  }

  return nodes
}

function createCurve(): Curve {
  const start = pick(grid.value)

  let end = pick(grid.value)

  while (end === start) {
    end = pick(grid.value)
  }

  const nodes =
    createNodes(start, end)

  return {
    start,
    end,
    nodes,

    progress: random(0, 1),

    duration: random(
      props.minDuration,
      props.maxDuration,
    ),

    /*
     * Start at a random point in the lifecycle.
     * This prevents all strings from appearing simultaneously.
     */
    opacity: random(0.25, 1),

    phase:
      Math.random() > 0.5
        ? 'alive'
        : 'appearing',
  }
}

function buildCurves() {
  curves.value = Array.from(
    { length: props.strings },
    createCurve,
  )
}

/* -------------------------------------------------------------------------- */
/* Curve regeneration                                                         */
/* -------------------------------------------------------------------------- */

function regenerate(curve: Curve) {
  curve.start = pick(grid.value)

  let end = pick(grid.value)

  while (end === curve.start) {
    end = pick(grid.value)
  }

  curve.end = end

  curve.nodes =
    createNodes(
      curve.start,
      curve.end,
    )

  curve.progress = 0

  curve.duration = random(
    props.minDuration,
    props.maxDuration,
  )

  curve.phase = 'appearing'
  curve.opacity = 0
}

/* -------------------------------------------------------------------------- */
/* Animation                                                                  */
/* -------------------------------------------------------------------------- */

function smoothstep(t: number) {
  return t * t * (3 - 2 * t)
}

function lerp(
  a: number,
  b: number,
  t: number,
) {
  return a + (b - a) * t
}

function updateGeometry(
  curve: Curve,
  dt: number,
) {
  curve.progress += dt / curve.duration

  if (curve.progress >= 1) {
    curve.progress = 0

    for (const node of curve.nodes) {
      node.from = {
        ...node.to,
      }

      node.to =
        makeMovingPoint(
          curve.start,
          curve.end,
          node.t,
        )
    }

    curve.duration = random(
      props.minDuration,
      props.maxDuration,
    )
  }

  const t =
    smoothstep(curve.progress)

  for (const node of curve.nodes) {
    node.current.x =
      lerp(
        node.from.x,
        node.to.x,
        t,
      )

    node.current.y =
      lerp(
        node.from.y,
        node.to.y,
        t,
      )
  }
}

function updateLifecycle(
  curve: Curve,
  dt: number,
) {
  /*
   * Appearing
   */
  if (curve.phase === 'appearing') {
    curve.opacity +=
      dt / props.fadeDuration

    if (curve.opacity >= 1) {
      curve.opacity = 1
      curve.phase = 'alive'
      curve.progress = 0
    }

    return
  }

  /*
   * Alive
   *
   * The string exists for a while and its geometry
   * continuously changes.
   */
  if (curve.phase === 'alive') {
    curve.opacity = 1

    curve.progress +=
      dt / curve.duration

    if (curve.progress >= 1) {
      /*
       * Instead of immediately replacing it,
       * start fading it away.
       */
      curve.phase = 'disappearing'
      curve.progress = 0
    }

    return
  }

  /*
   * Disappearing
   */
  if (curve.phase === 'disappearing') {
    curve.opacity -=
      dt / props.fadeDuration

    if (curve.opacity <= 0) {
      curve.opacity = 0

      /*
       * The old string is now completely invisible.
       * Only now do we give it new endpoints and nodes.
       */
      regenerate(curve)
    }
  }
}

let previousTime: number | undefined

function animate(time: number) {
  if (previousTime === undefined) {
    previousTime = time
  }

  const dt =
    Math.min(
      time - previousTime,
      50,
    )

  previousTime = time

  for (const curve of curves.value) {
    /*
     * Geometry continues changing while the string
     * is visible.
     */
    if (
      curve.phase === 'alive' ||
      curve.phase === 'appearing'
    ) {
      updateGeometry(curve, dt)
    }

    updateLifecycle(curve, dt)
  }

  animationFrame =
    requestAnimationFrame(animate)
}

/* -------------------------------------------------------------------------- */
/* SVG path                                                                   */
/* -------------------------------------------------------------------------- */

function pointsFor(curve: Curve): Point[] {
  return [
    curve.start,
    ...curve.nodes.map(
      node => node.current,
    ),
    curve.end,
  ]
}

function pathData(curve: Curve) {
  const points =
    pointsFor(curve)

  if (points.length < 2)
    return ''

  let d =
    `M ${points[0].x} ${points[0].y}`

  /*
   * Catmull-Rom style spline converted into
   * cubic Bézier segments.
   */
  for (
    let i = 0;
    i < points.length - 1;
    i++
  ) {
    const p0 =
      points[i - 1] ?? points[i]

    const p1 =
      points[i]

    const p2 =
      points[i + 1]

    const p3 =
      points[i + 2] ?? p2

    const c1 = {
      x:
        p1.x +
        (p2.x - p0.x) / 6,

      y:
        p1.y +
        (p2.y - p0.y) / 6,
    }

    const c2 = {
      x:
        p2.x -
        (p3.x - p1.x) / 6,

      y:
        p2.y -
        (p3.y - p1.y) / 6,
    }

    d += `
      C
      ${c1.x} ${c1.y},
      ${c2.x} ${c2.y},
      ${p2.x} ${p2.y}
    `
  }

  return d
}

/* -------------------------------------------------------------------------- */
/* Grid rendering                                                             */
/* -------------------------------------------------------------------------- */

const gridLines = computed(() => {
  const lines: Array<[
    GridPoint,
    GridPoint,
  ]> = []

  const map =
    new Map<string, GridPoint>()

  for (const p of grid.value) {
    map.set(
      `${p.gx},${p.gy},${p.gz}`,
      p,
    )
  }

  const get = (
    x: number,
    y: number,
    z: number,
  ) =>
    map.get(`${x},${y},${z}`)

  for (const p of grid.value) {
    const neighbours = [
      get(
        p.gx + 1,
        p.gy,
        p.gz,
      ),

      get(
        p.gx,
        p.gy + 1,
        p.gz,
      ),

      get(
        p.gx,
        p.gy,
        p.gz + 1,
      ),
    ]

    for (const q of neighbours) {
      if (q) {
        lines.push([p, q])
      }
    }
  }

  return lines
})

/* -------------------------------------------------------------------------- */
/* Lifecycle                                                                  */
/* -------------------------------------------------------------------------- */

watch(
  () => [
    props.gridX,
    props.gridY,
    props.gridZ,
    props.spacing,
    props.angle,
    props.depthScale,
    props.strings,
  ],
  () => {
    buildGrid()
    buildCurves()
  },
)

onMounted(() => {
  buildGrid()
  buildCurves()

  animationFrame =
    requestAnimationFrame(animate)
})

onUnmounted(() => {
  if (animationFrame !== undefined) {
    cancelAnimationFrame(animationFrame)
  }
})
</script>

<template>
  <svg
    class="curved-grid"
    :viewBox="`0 0 ${width} ${height}`"
    preserveAspectRatio="xMidYMid meet"
    xmlns="http://www.w3.org/2000/svg"
    aria-hidden="true"
  >
    <!-- Isometric grid -->
    <g
      fill="none"
      class="light:stroke-[var(--slate-100)] dark:stroke-[var(--slate-10)]"
      :opacity="gridOpacity"
      stroke-width="0.5"
    >
      <line
        v-for="(line, i) in gridLines"
        :key="`grid-line-${i}`"
        :x1="line[0].x"
        :y1="line[0].y"
        :x2="line[1].x"
        :y2="line[1].y"
      />
    </g>

    <!-- Grid points -->
    <g
      class="light:stroke-[var(--slate-100)] dark:stroke-[var(--slate-10)]"

      :opacity="gridOpacity + 0.2"
    >
      <circle
        v-for="(point, i) in grid"
        :key="`grid-point-${i}`"
        :cx="point.x"
        :cy="point.y"
        :r="pointSize"
      />
    </g>

    <!-- Animated strings -->
    <g
      fill="none"
      :stroke="color"
      :stroke-width="strokeWidth"
      stroke-linecap="round"
    >
      <path
        v-for="(curve, i) in curves"
        :key="`string-${i}`"
        :d="pathData(curve)"
        :opacity="curve.opacity * opacity"
      />
    </g>
  </svg>
</template>

<style scoped>
.curved-grid {
  display: block;
  width: 100%;
  height: 100%;
  overflow: visible;
  pointer-events: none;
}

path,
line,
circle {
  vector-effect: non-scaling-stroke;
}
</style>
