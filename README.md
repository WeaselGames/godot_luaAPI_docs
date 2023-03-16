# luaAPI Documentation
This repo contains the source for our static documentation site hosted with github pages.
We are using the mkdocs static site generator with mike. To build and run locally following:
- Download and install python
- Install mkdocs and mike: `pip install mkdocs-material mike setuptools`.
- Run `mike deploy [version]` to build the static site. (replace `[version]` with the current version you would like to build for I.E. v2.0-alpha).
- Finally, run `mike serve` to serve the site locally.
You can now visit it at: http://localhost:8000