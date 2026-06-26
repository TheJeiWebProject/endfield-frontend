<template>
  <svg
    xmlns="http://www.w3.org/2000/svg"
    :width="size"
    :height="size"
    :viewBox="viewBox"
    :fill="color === 'none' ? 'none' : 'none'"
    :stroke="color !== 'none' ? color : 'none'"
    :stroke-width="strokeWidth"
    :stroke-linecap="strokeLinecap"
    :stroke-linejoin="strokeLinejoin"
    :class="className"
    :style="{ display: 'inline-block', verticalAlign: 'middle', flexShrink: 0 }"
  >
    <template v-if="name === 'book'">
      <path d="M4 6h16v14H4z" stroke-width="1.5" />
      <path d="M4 6h8v14H4z" fill="currentColor" opacity="0.15" />
      <path d="M8 10h6M8 13h4" />
      <path d="M4 20h16" stroke-width="1.5" />
    </template>

    <template v-else-if="name === 'box'">
      <path d="M12 2L2 7l10 5 10-5-10-5z" />
      <path d="M2 17l10 5 10-5" />
      <path d="M2 12l10 5 10-5" />
      <path d="M12 2v10" />
      <path d="M12 12v10" />
    </template>

    <template v-else-if="name === 'microscope'">
      <circle cx="12" cy="10" r="4" />
      <path d="M12 6V3M9 3h6" />
      <path d="M5 21h14M9 14v7M15 14v7M3 11h4l2 10h6l2-10h4" />
      <path d="M20 7a2 2 0 0 0-2-2h-1v4h1a2 2 0 0 0 2-2z" />
    </template>

    <template v-else-if="name === 'calculator'">
      <rect x="4" y="2" width="16" height="20" rx="2" />
      <path d="M8 6h8M8 10h8M8 14h2M13 14h3M8 17h2M13 17h3" />
    </template>

    <template v-else-if="name === 'palette'">
      <circle cx="12" cy="12" r="10" />
      <path d="M12 2a10 10 0 0 1 0 20c-1.5 0-2.5-1-2.5-2.5 0-.6.2-1.1.5-1.5.3-.4.5-.9.5-1.5 0-1.4-1.1-2.5-2.5-2.5a10 10 0 0 1 0-12" fill="currentColor" opacity="0.15" />
      <circle cx="8.5" cy="10" r="1.2" />
      <circle cx="15.5" cy="7" r="1.2" />
      <circle cx="17.5" cy="12" r="1.2" />
    </template>

    <template v-else-if="name === 'clipboard'">
      <rect x="5" y="3" width="14" height="18" rx="2" />
      <path d="M9 3a2 2 0 0 1 2-2h2a2 2 0 0 1 2 2v0a2 2 0 0 1-2 2h-2a2 2 0 0 1-2-2z" />
      <path d="M8 10h8M8 14h8M8 18h6" />
    </template>

    <template v-else-if="name === 'close'">
      <path d="M18 6L6 18M6 6l12 12" stroke-width="2" />
    </template>

    <template v-else-if="name === 'check'">
      <path d="M5 13l4 4L19 7" stroke-width="2.5" />
    </template>

    <template v-else-if="name === 'gear'">
      <circle cx="12" cy="12" r="3" />
      <path d="M12 1v3M12 20v3M4.2 4.2l2.1 2.1M17.7 17.7l2.1 2.1M1 12h3M20 12h3M4.2 19.8l2.1-2.1M17.7 6.3l2.1-2.1" />
    </template>

    <template v-else-if="name === 'chartBar'">
      <path d="M3 20h18" stroke-width="1.5" />
      <rect x="4" y="14" width="3" height="6" rx="0.5" />
      <rect x="10" y="10" width="3" height="10" rx="0.5" />
      <rect x="16" y="6" width="3" height="14" rx="0.5" />
    </template>

    <template v-else-if="name === 'chartLine'">
      <path d="M3 20h18" stroke-width="1.5" />
      <path d="M4 16l4-6 4 4 4-8 4 6" stroke-width="1.8" />
      <circle cx="8" cy="10" r="1.2" />
      <circle cx="12" cy="14" r="1.2" />
      <circle cx="16" cy="6" r="1.2" />
    </template>

    <template v-else-if="name === 'save'">
      <path d="M19 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h11l5 5v11a2 2 0 0 1-2 2z" />
      <path d="M17 21v-8H7v8M7 3v5h8" />
    </template>

    <template v-else-if="name === 'folder'">
      <path d="M3 7a2 2 0 0 1 2-2h4l2 2h6a2 2 0 0 1 2 2v8a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V7z" />
    </template>

    <template v-else-if="name === 'ruler'">
      <path d="M21.3 15.3a1 1 0 0 1 0 1.4l-4.6 4.6a1 1 0 0 1-1.4 0L2.7 8.7a1 1 0 0 1 0-1.4l4.6-4.6a1 1 0 0 1 1.4 0z" />
      <path d="M8 8l1 1M11 5l1 1M14 8l1 1M5 11l1 1M9 15l1 1M13 18l1 1" />
    </template>

    <template v-else-if="name === 'search'">
      <circle cx="10.5" cy="10.5" r="6" />
      <path d="M15 15l5 5" stroke-width="2" />
    </template>

    <template v-else-if="name === 'refresh'">
      <path d="M3 12a9 9 0 0 1 9-9 9.8 9.8 0 0 1 6 2.5L21 8" />
      <path d="M21 3v5h-5" />
      <path d="M21 12a9 9 0 0 1-9 9 9.8 9.8 0 0 1-6-2.5L3 16" />
      <path d="M3 21v-5h5" />
    </template>

    <template v-else-if="name === 'droplet'">
      <path d="M12 2.69l5.66 5.66a8 8 0 1 1-11.31 0z" />
    </template>

    <template v-else-if="name === 'trash'">
      <path d="M4 6h16M6 6v12a2 2 0 0 0 2 2h8a2 2 0 0 0 2-2V6M9 6V4a1 1 0 0 1 1-1h4a1 1 0 0 1 1 1v2" />
      <path d="M10 10v6M14 10v6" />
    </template>

    <template v-else-if="name === 'factory'">
      <path d="M3 21h18" stroke-width="1.5" />
      <path d="M5 21V7l8-4v18" />
      <path d="M19 21V11l-4 2v8" />
      <rect x="8" y="9" width="3" height="5" rx="0.5" />
      <rect x="14" y="6" width="3" height="5" rx="0.5" />
    </template>

    <template v-else-if="name === 'mountain'">
      <path d="M2 18L8 8l4 6 4-8 6 12H2z" fill="currentColor" opacity="0.15" />
      <path d="M2 18L8 8l4 6 4-8 6 12" />
    </template>

    <template v-else-if="name === 'snowflake'">
      <path d="M12 2v4M12 18v4M4.9 4.9l2.8 2.8M16.3 16.3l2.8 2.8M2 12h4M18 12h4M4.9 19.1l2.8-2.8M16.3 7.7l2.8-2.8" stroke-width="1.5" />
    </template>

    <template v-else-if="name === 'star'">
      <path d="M12 2l3.09 6.26L22 9.27l-5 4.87L18.18 22 12 18.56 5.82 22 7 14.14 2 9.27l6.91-1.01z" />
    </template>

    <template v-else-if="name === 'starEmpty'">
      <path d="M12 2l3.09 6.26L22 9.27l-5 4.87L18.18 22 12 18.56 5.82 22 7 14.14 2 9.27l6.91-1.01z" />
    </template>

    <template v-else-if="name === 'zap'">
      <path d="M13 2L3 14h7l-1 8 10-12h-7l1-8z" />
    </template>

    <template v-else-if="name === 'link'">
      <path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71" />
      <path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71" />
    </template>

    <template v-else-if="name === 'tree'">
      <path d="M12 21v-5M7 16l5-9 5 9" />
      <path d="M12 7v2" stroke-width="2" />
      <path d="M16 16H8" />
    </template>

    <template v-else-if="name === 'lightbulb'">
      <path d="M9 18h6M10 21h4" />
      <path d="M12 2a7 7 0 0 0-7 7c0 2.4 1.2 4.5 3 5.7V17a2 2 0 0 0 2 2h4a2 2 0 0 0 2-2v-2.3c1.8-1.3 3-3.4 3-5.7a7 7 0 0 0-7-7z" />
    </template>

    <template v-else-if="name === 'arrowRight'">
      <path d="M5 12h14M13 6l6 6-6 6" stroke-width="2" />
    </template>

    <template v-else-if="name === 'party'">
      <path d="M12 2l2.5 6.5L21 9l-5 4.5L18 20l-6-3.5L6 20l2-6.5L3 9l6.5-.5z" />
    </template>

    <template v-else-if="name === 'warning'">
      <path d="M12 9v4M12 17h.01" stroke-width="2" stroke-linecap="round" />
      <path d="M10.3 3.9L2 18a1.7 1.7 0 0 0 1.5 2.5h17a1.7 1.7 0 0 0 1.5-2.5L13.7 3.9a1.7 1.7 0 0 0-3.4 0z" />
    </template>

    <template v-else-if="name === 'chevronDown'">
      <path d="M6 9l6 6 6-6" stroke-width="2.5" />
    </template>

    <template v-else-if="name === 'menu'">
      <path d="M4 6h16M4 12h16M4 18h16" stroke-width="2.5" />
    </template>

    <template v-else-if="name === 'home'">
      <path d="M3 12l9-9 9 9" />
      <path d="M5 10v9a1 1 0 0 0 1 1h3v-5h6v5h3a1 1 0 0 0 1-1v-9" />
    </template>

    <template v-else-if="name === 'plus'">
      <path d="M12 5v14M5 12h14" stroke-width="2" />
    </template>

    <template v-else-if="name === 'minus'">
      <path d="M5 12h14" stroke-width="2" />
    </template>

    <template v-else-if="name === 'external'">
      <path d="M18 13v6a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V8a2 2 0 0 1 2-2h6" />
      <path d="M15 3h6v6M10 14L21 3" />
    </template>

    <template v-else-if="name === 'grid'">
      <rect x="3" y="3" width="7" height="7" rx="1" />
      <rect x="14" y="3" width="7" height="7" rx="1" />
      <rect x="3" y="14" width="7" height="7" rx="1" />
      <rect x="14" y="14" width="7" height="7" rx="1" />
    </template>

    <template v-else-if="name === 'layout'">
      <rect x="3" y="3" width="7" height="18" rx="1" />
      <rect x="14" y="3" width="7" height="7" rx="1" />
      <rect x="14" y="14" width="7" height="7" rx="1" />
    </template>

    <template v-else-if="name === 'filter'">
      <path d="M4 6h16M7 12h10M10 18h4" stroke-width="2.5" />
    </template>
  </svg>
</template>

<script setup>
defineProps({
  name: { type: String, required: true },
  size: { type: [Number, String], default: 20 },
  color: { type: String, default: 'currentColor' },
  strokeWidth: { type: [Number, String], default: 1.8 },
  strokeLinecap: { type: String, default: 'round' },
  strokeLinejoin: { type: String, default: 'round' },
  className: { type: String, default: '' },
})

const viewBox = '0 0 24 24'
</script>
