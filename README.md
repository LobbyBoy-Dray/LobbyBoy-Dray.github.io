# 🧑‍💻 Personal Website

This is [Jingwei Gao](https://LobbyBoy-Dray.github.io)'s personal website:

- Made with [AstroPaper 📄](https://github.com/satnaing/astro-paper) by [Sat Naing](https://satnaing.dev) and [contributors](https://github.com/satnaing/astro-paper/graphs/contributors).
- Deployed to [GitHub Pages](https://docs.astro.build/en/guides/deploy/github/).

## Modification | Memo

- All featured posts are displayed together on the index page.
- `src/config.ts`→`recentPostSlot`: #displayed recent posts.
- How to restore the **About** page:
  - Remove the comments regarding "about" in `src/components/Header.astro`.
  - Add file `src/pages/about.md` and `src/layouts/AboutLayout.astro`.
- Add support for Math:
  - Install `remark-math@5.1.1`, a plugin to support a math syntax in markdown (⚠️ the latest version will raise an [exception](https://github.com/remarkjs/remark-math/issues/89)).
  - Install `rehype-katex`, a plugin to render math in HTML with KaTeX.
  - Modify `astro.config.ts` and `src/layouts/Layout.astro`.
