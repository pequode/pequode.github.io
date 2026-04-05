# pequode.github.io

My portfolio website showcasing professional and personal projects in scientific computing, MLOps, and infrastructure engineering.

Built with [Jekyll](https://jekyllrb.com/) using the [Minima](https://github.com/jekyll/minima) theme and hosted on [GitHub Pages](https://pages.github.com/).

## Local Development

### Prerequisites
- [Ruby](https://www.ruby-lang.org/en/documentation/installation/) (>= 2.5)
- [Bundler](https://bundler.io/)

### Setup
```bash
gem install bundler jekyll
bundle install
```

### Run locally
```bash
bundle exec jekyll serve
```
Then open [http://localhost:4000](http://localhost:4000).

## Project Structure

```
├── _config.yml          # Jekyll configuration
├── _projects/           # Project pages (Jekyll collection)
├── assets/
│   ├── css/             # Stylesheets
│   ├── auto_archiver/   # Auto-archive project images
│   └── scripts/pkg/     # WASM package for maze generator
└── index.html           # Home page with project grid
```

## Deployment

Pushes to `main` are automatically deployed via GitHub Pages.

## License

[MIT](LICENSE)
