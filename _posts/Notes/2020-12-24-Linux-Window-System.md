---
title: Linux Window System
typora-root-url: ../..
---

# Linux Window Manager / Display Manager / Display Server

**Display Protocol**:

- X11
- Wayland (Protocol)

**Windowing System**:

- X Window System
- Qt Extended
- Wayland

**Widget Toolkit**:

- GTK (formerly GTK+, GIMP Toolikt)
  - most popular toolkits for the [Wayland](https://en.wikipedia.org/wiki/Wayland_(display_server_protocol)) and [X11](https://en.wikipedia.org/wiki/X_Window_System_core_protocol) [windowing systems](https://en.wikipedia.org/wiki/Windowing_system)
- Qt

**Window Managers**:

- Mutter (the Wayland compositor and X Window Manager)
  - default in GNOME 3 DE
  - use GTK+, Wayland (X Window System)
- Compiz (use X Window System)
  - use GTK toolkit, X Window System
- Metacity (replaced by Mutter)

**Display Server** (Protocol Compositor):

- Mutter (the Wayland compositor)
- Quartz Compositor (for MacOS)
- X.Org Server

**Display Manager**:

- GDM (GNOME Display Manager)
- XDM (X display manager)

**Desktop Environments/Graphical Interface** :

- GNOME (Shell) (1/2/3) (using GTK toolkit)
- LXDE
- KDE Plasma
- Unity (a graphical shell for the GNOME desktop environment. Don't be confused with a similar thing, Ubuntu Unity, which is a distro instead)
- Enlightenment
- Xfce
- LXDE (using GTK toolkit)
- LXQT (using Qt toolkit, default in Lubuntu)

![What is the difference between a desktop environment and a window manager?  - Ask Ubuntu](/assets/images/LZGBJ.png)

![Arch Linux Install Guide - Step 2: Desktop Environment Installation -  TurluCode](/assets/images/linux_basic_components_of_a_gui.png)