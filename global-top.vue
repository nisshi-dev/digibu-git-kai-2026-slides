<!-- 全スライド共通のフッター（表紙と、frontmatter に footer: false を書いたスライドでは出さない） -->
<template>
  <div
    v-if="$nav.currentPage > 1 && $nav.currentSlideRoute?.meta?.slide?.frontmatter?.footer !== false"
    class="absolute bottom-4 left-14 right-14 flex justify-between text-[0.65rem] tracking-widest pointer-events-none"
    style="color: var(--muted)"
  >
    <span>GitHubをちょっと触ってみる会</span>
    <span>{{ $nav.currentPage }} / {{ $nav.total }}</span>
  </div>
</template>
