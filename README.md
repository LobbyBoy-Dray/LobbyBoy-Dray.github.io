# 🧑‍💻 Personal Website

This is [Jingwei Gao](https://LobbyBoy-Dray.github.io)'s personal website:

- Made with [AstroPaper 📄](https://github.com/satnaing/astro-paper) by [Sat Naing](https://satnaing.dev) and [contributors](https://github.com/satnaing/astro-paper/graphs/contributors).
- Deployed to [GitHub Pages](https://docs.astro.build/en/guides/deploy/github/).

## Modification | Memo

- All featured posts are displayed together on the index page.
- `src/config.ts`→`recentPostSlot`: #displayed recent posts.
- How to restore the **About** page:
  - Remove the comments regarding "about" in `src/components/Header.astro`;
  - Add file `src/pages/about.md` and `src/layouts/AboutLayout.astro`.
