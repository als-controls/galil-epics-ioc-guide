# Galil EPICS IOC User's Guide

User's guide for [Mark Clift's Galil EPICS IOC](https://github.com/motorapp/Galil) (V4.x driver line). Maps every EPICS PV in the IOC to its corresponding Galil 2-letter command, with configuration details, architecture notes, and a command reference appendix.

Built with [MkDocs](https://www.mkdocs.org/) using the [Material](https://squidfunk.github.io/mkdocs-material/) theme.

## Reading the Guide

Start here: [docs/index.md](docs/index.md)

## Building Locally

```bash
python3 -m venv venv
source venv/bin/activate
pip install mkdocs mkdocs-material
mkdocs serve
```

Then open http://127.0.0.1:8000.

To build static HTML into the `site/` directory:

```bash
mkdocs build
```
