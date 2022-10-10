---
layout: default
title: Using Typescript
parent: Guide
nav_order: 9
---

# Using Typescript

Since version `1.0.0-beta8.2` Ayakashi supports generating and running typescript projects.

## Generating new projects

Running `ayakashi new` will ask if you want to generate a typescript or javascript project. You can also pass the `--js` or `--ts` flags to disable the prompt.

## Generating new scrapers, scripts, actions etc

The generator will automatically infer if it's a javascript or typescript project and create the appropriate files.

## Running typescript projects

Running typescript projects is done in the same way as with javascript projects.  
Run `ayakashi run` in the **project root** and the runner will compile the ts files and then run the project.  
**Note**: If you have already built the ts files, eg by having `tsc --watch` running in a separate terminal window, you can pass the `--skipTsBuild` flag to disable auto-compilation.

## Converting older projects

The typescript runner expects the project to be structured in a standard fashion.  
The easiest way to convert older projects is to generate a new project, use scaffold generators for your components and then copy your code over while rewriting to ts.
