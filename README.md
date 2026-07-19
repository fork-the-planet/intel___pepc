<!--
-*- coding: utf-8 -*-
vim: ts=4 sw=4 tw=100 et ai si

# Copyright (C) 2020-2026 Intel Corporation
# SPDX-License-Identifier: BSD-3-Clause

Author: Artem Bityutskiy <artem.bityutskiy@linux.intel.com>
-->

# pepc

## Table of Contents

- [pepc](#pepc)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [Disclaimer](#disclaimer)
  - [Privileges](#privileges)
  - [Requirements](#requirements)
  - [Installation](#installation)
  - [User Guide](#user-guide)
  - [Repository Tools](#repository-tools)
  - [Contributing](#contributing)
  - [Man pages](#man-pages)
  - [Helpful resources](#helpful-resources)
  - [FAQ](#faq)
    - [What to do if my platform is not supported?](#what-to-do-if-my-platform-is-not-supported)

## Introduction

`pepc`, short for "Power, Energy, and Performance Configurator", is a command-line tool designed for
reading and changing power management features. For example, `pepc` can be used to modify CPU
or uncore frequency limits for all or a subset of CPUs, cores, modules, dies, or packages in the
system.

`pepc` consolidates power management configuration in one tool with a consistent and convenient
command-line interface instead of using various tools, such as `cpupower`, `rdmsr`,
`cat /sys/...`, etc.

## Disclaimer

This is not an official Intel product. It is an open-source tool developed by Intel engineers to
facilitate power and performance configuration in Linux in lab environments. Please use it
at your own risk.

## Privileges

Some `pepc` read operations may be done without superuser privileges, some require superuser
privileges (root). This depends on the specific operation and the underlying mechanism. For example,
reading CPU frequency limits from sysfs does not need root, therefore `pepc pstates info --min-freq`
can be run as a normal user. On the other hand, reading an MSR (Model Specific Register) needs
superuser privileges, so something like `pepc cstates info --pkg-cstate-limit` needs to be run as
root. See [Superuser Privileges](docs/guide-main.md#superuser-privileges) for more details.

## Requirements

- `pepc` requires Python 3.9 or newer.
- Many options need access to MSRs (Model Specific Registers), requiring the `msr` kernel driver.
  Ensure the `msr` kernel driver is available, as some Linux distributions may disable it by
  default.

## Installation

Please refer to the [Installation Guide](docs/guide-install.md) document.

## User Guide

Please refer to the [User Guide](docs/guide-main.md) document.

## Repository Tools

Only `pepc` itself is installed. The remaining tools are development and maintenance utilities that
must be run from the repository.

- `pepc`: the main tool, the only one that gets installed.
- `tools/install-pepc`: installs `pepc` on a local or remote host into a Python virtual environment.
- `tools/emulation-data-generator`: collects data from a real system to produce emulation datasets
  for testing. See [Emulation Data](docs/guide-main.md#emulation-data) and
  [Tests Guide](docs/guide-tests.md).
- `tools/msr-ioscope`: determines the I/O scope (package, die, core, CPU) of MSR bits on a real
  system.
  See [MSR Scope](https://github.com/dedekind/dedekind-share/blob/master/2025/msr-scope.md).
- `tools/make-standalone`: packages `pepc` and its dependencies into a single standalone executable.
- `tools/tpmi-spec-files-generator`: generates TPMI specification XML files from raw data.
- `tools/make-a-release`: prepares a new `pepc` release (changelog, version bump, commit, tag).

## Contributing

Coding style and conventions are documented in [CONTRIBUTING.md](CONTRIBUTING.md). An
AI-assistant-oriented condensed version is available in
[copilot-instructions.md](.github/copilot-instructions.md).

## Man pages

Here are the manual pages for all `pepc` features. They are also installed along with `pepc` and can
be accessed via the `man` command (e.g., `man pepc-cstates`).

- CPU P-states: [man page](docs/man/pepc-pstates.md)
- CPU C-states: [man page](docs/man/pepc-cstates.md)
- Uncore properties: [man page](docs/man/pepc-uncore.md)
- CPU onlining and offlining: [man page](docs/man/pepc-cpu-hotplug.md)
- CPU topology: [man page](docs/man/pepc-topology.md)
- PM QoS: [man page](docs/man/pepc-pmqos.md)
- TPMI: [man page](docs/man/pepc-tpmi.md)
- ASPM: [man page](docs/man/pepc-aspm.md)

Some features are hardware-agnostic, while others depend on specific hardware capabilities.

## Helpful resources

- [Intel CPU Base Frequency Explained](https://github.com/dedekind/dedekind-share/blob/master/2026/cpu-base-freq.md):
  explains the concept of CPU base frequency and many CPU performance scaling topics.
- [Intel C-state namespaces](https://github.com/dedekind/dedekind-share/blob/master/2025/cstate-namespaces.md):
  explains C-state naming conventions.
- [Xeon C6P and C6SP Idle States](https://github.com/dedekind/dedekind-share/blob/master/2025/c6p-c6sp.md):
  explains the C6P and C6SP idle states on Intel Xeon platforms.

- [Uncore ELC and Frequency Scaling](https://github.com/dedekind/dedekind-share/blob/master/2025/uncore-elc.md):
  explains the uncore ELC mechanism.
- [MSR scope](https://github.com/dedekind/dedekind-share/blob/master/2025/msr-scope.md):
  explains the concept of MSR scope (per-core, per-module, per-package) and related pitfalls.

## FAQ

### What to do if my platform is not supported?

Some `pepc` features (e.g., `--pkg-cstate-limit`) are implemented only for certain Intel platforms.
This means that we verified the feature on a limited number of platforms, not that it is
unsupported by other platforms. To be on the safe side, we refuse to change the underlying MSR
registers on platforms we did not verify.

If `pepc` fails with a message like "this feature is not supported on this platform" for you, feel
free to contact the authors with a request. Often this can be resolved by simply adding a CPU ID to
the list of supported platforms, and you may be able to do this yourself and send a pull request.
