# OKFBase

Public catalog of [OKF](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md) (Open Knowledge Format) knowledge packs for [Boone-CLI](https://github.com/DiurnalProductions/Boone-cli), the command-line knowledge-base viewer.

This repository does not host knowledge content. It publishes `catalog.json` — a registry of links to standalone OKF pack repositories that Boone downloads and installs.

## How it works

1. Knowledge packs live in their own GitHub repos (e.g. `typescript-okf`).
2. Each pack contains `okf.json`, `index.md`, and `concepts/*.md` files.
3. Boone fetches `catalog.json` from this repo and presents the listed packs in its catalog screen.
4. Users install packs directly from those source repositories.

```
catalog.json  →  Boone  →  typescript-okf, css-okf, …
```

The catalog is served from the `main` branch:

`https://raw.githubusercontent.com/DiurnalProductions/OKFBase/main/catalog.json`

## Listed packs

| Pack | Repository |
|------|------------|
| CSS | [css-okf](https://github.com/DiurnalProductions/css-okf) |
| HTML | [html-okf](https://github.com/DiurnalProductions/html-okf) |
| JavaScript | [javascript-okf](https://github.com/DiurnalProductions/javascript-okf) |
| Next.js | [nextjs-okf](https://github.com/DiurnalProductions/nextjs-okf) |
| Python | [python-okf](https://github.com/DiurnalProductions/python-okf) |
| React | [react-okf](https://github.com/DiurnalProductions/react-okf) |
| React Native | [react-native-okf](https://github.com/DiurnalProductions/react-native-okf) |
| TypeScript | [typescript-okf](https://github.com/DiurnalProductions/typescript-okf) |

## Contributing

To publish a new pack or update catalog metadata, see **[CONTRIBUTING.md](CONTRIBUTING.md)**. It covers:

- OKF pack repository structure and format
- How to add an entry to `catalog.json`
- A generic LLM prompt template for generating new packs
- A filled TypeScript example prompt ([typescript-okf](https://github.com/DiurnalProductions/typescript-okf))

## Support

Diurnal Productions builds Boone, OKFBase, and the knowledge packs as free, open resources. If you find them valuable, consider a donation to help cover hosting and development:

**[Donate via PayPal](https://www.paypal.com/donate/?hosted_button_id=ENRPE663LJQZW)**

## License

See [LICENSE](LICENSE).
