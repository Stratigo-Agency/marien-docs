# Marien Docs

The guides for running screens on Marien. Published at **https://docs.marien.co.id/** — it rebuilds itself about a minute after any change here lands on `main`.

## Editing a page (no tools needed)

1. Open the page on the site and click the **pencil** at the top right. (Or open the file under `docs/` here on GitHub and click the pencil.)
2. Change the text. The **Preview** tab shows how it will look.
3. Press **Commit changes** — leave the options as they are.

That's it. About a minute later the site shows your change. You need a free GitHub account and to be a collaborator on this repo — ask Bryan to add you.

## Writing tips

Pages are Markdown. The few things worth knowing:

- `# Title` once at the top; `## Section` and `### Sub-section` for headings.
- `1.` `2.` `3.` for numbered steps. To add a note under a step, indent the next line by four spaces.
- Commands go between triple backticks: ```` ```bash ```` … ```` ``` ````.
- A highlighted box:

  ```
  !!! note "Optional title"
      The text, indented by four spaces.
  ```

  Use `note`, `tip`, `info` or `warning`.

- A link to another guide: `[Connecting a Screen](connecting-a-screen.md)`.

## Two languages

The site is in English (`docs.marien.co.id/`) and Indonesian (`docs.marien.co.id/id/`); readers switch with the language icon in the header. Every page is a pair of files: `creating-a-campaign.md` is English, `creating-a-campaign.id.md` is its Indonesian twin. When you change one, change the other. Indonesian copy uses "kamu", never "Anda", and keeps button names exactly as they appear in Marien CMS (which is in English), e.g. klik **Connect a screen**.

## Adding a page

1. Add a new `.md` file in `docs/` (all lowercase, hyphens for spaces, e.g. `power-schedules.md`) and its Indonesian twin `power-schedules.id.md`.
2. Add it to the `nav` list in `mkdocs.yml` so it appears in the sidebar, and its Indonesian title under `nav_translations`.
3. Add a row for it on `docs/index.md` and `docs/index.id.md`.

## Adding an image

Drag the image into the `docs/` folder on GitHub (or a `docs/images/` folder), then in the page write `![What it shows](images/the-file.png)`.

## If the site didn't update

Look at the **Actions** tab. A red run means the build failed — usually a link to a page that doesn't exist, or a page not listed in `nav`. The run's log says which.
