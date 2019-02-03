# Naming

This document lists several conventions for naming things in Hitchy.

## Files

Names of files should be considered case-insensitive. This is designed to make Hitchy independent of filesystem used to run Hitchy. For example, Windows is case-insensitive whereas Linux is case-sensitive. Developing an application under Windows for production use under Linux might result in severe issues when considering filenames case-sensitively. That's why filenames are considered using kebab-case names so the actual case of letters won't matter and still it's possible to derive case-sensitive names from it.

In conclusion file names

* are case-insensitive and
* use dashes to put structure in a name.

Examples considered equal are:

* `some-module.js`
* `Some-Module.js`
