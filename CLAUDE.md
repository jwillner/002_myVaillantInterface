# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

This project documents the development of a self-built interface for the eBUS of a Vaillant heating system. The goal is a clean foundation for later capturing, analyzing, and potentially integrating eBUS data into a smart home system.

The repository currently contains only the project structure and documentation — no implementation exists yet.

## Repository structure

- `docs/` — project documentation
- `firmware/` — future firmware for the interface
- `hardware/` — hardware documentation and schematics
- `home-assistant/` — future Home Assistant integration
- `notes/` — notes and research findings

All of these directories are currently empty. There are no build, lint, or test commands yet since no code has been written. When code is added to one of these areas (e.g. firmware source, a Home Assistant custom component), update this file with the relevant build/test/run commands and architecture notes for that subsystem.
