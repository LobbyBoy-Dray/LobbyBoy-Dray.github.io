# 🧑‍💻 Personal Website

This is [Jingwei Gao](https://LobbyBoy-Dray.github.io)'s personal website:

- Made with [AstroPaper 📄](https://github.com/satnaing/astro-paper) by [Sat Naing](https://satnaing.dev) and [contributors](https://github.com/satnaing/astro-paper/graphs/contributors).
- Deployed to [GitHub Pages](https://docs.astro.build/en/guides/deploy/github/).

## 💡 To-Do List

- Sidebar for TOB.

## 🆕 New Features

- Add support for math:
  - Install `remark-math@5.1.1`, a plugin to support a math syntax in markdown
    - ⚠️ the latest version will raise an [exception](https://github.com/remarkjs/remark-math/issues/89).
  - Install `rehype-katex`, a plugin to render math in HTML with KaTeX.
  - Modify `astro.config.ts` and `src/layouts/Layout.astro`.
- [2023.12.22] Add support for nested posts: [demo](https://lobbyboy-dray.github.io/posts/python-pd/)
  - A new item called "hidden" was added to the front matter and set as "true" for inner posts. Hence, these inner posts will not be visible directly and can only be accessed through the link in outer posts.
- [2023.12.25] Add a comment system powered by [giscus](https://giscus.app/zh-CN)
  - It is not easy to achieve perfect compatibility with the mode switch (light/dark).

## 🐛 Fixed Bugs

- 🐛 Equations in dark mode are now readable: [/_ KaTex _/](./src/styles/base.css)

## 📝 Memo

- All featured posts are displayed together on the index page.
- `src/config.ts`→`recentPostSlot`: #displayed recent posts.
- How to restore the **About** page:
  - Remove the comments regarding "about" in `src/components/Header.astro`.
  - Add file `src/pages/about.md` and `src/layouts/AboutLayout.astro`.
- Displayed equation: `$$a+b=c$$` doesn't work! Try `$$[Enter]a+b=c[Enter]$$` instead!
