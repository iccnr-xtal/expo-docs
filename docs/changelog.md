# Changelog

<!-- Copied from CHANGELOG.md of the EXPO repository by scripts/make_download_page.py: do not edit it by hand. -->

All notable changes to EXPO, for each released version.

## [2.3.15] - 2026-11-24

### Fixed
- Windows: crash at startup ("QOpenGLWidget: Failed to create wrapper texture") on systems without an adequate OpenGL driver, e.g. virtual machines without 3D acceleration. The software OpenGL renderer is used again as fallback.

## [2.3.14] - 2026-11-24

### Added
- `CONTACTS` directive of `%sanneal`: prints in the output file the intermolecular contacts of each final model, listed as clashes, short contacts and contacts according to the overlap (sum of the van der Waals radii − distance), followed by a summary table with the number of contacts of each model. The contacts involving hydrogen atoms are skipped by default (`noh`).
- Square root intensity scale for the pattern plot (Plot Style dialog), besides linear and logarithmic.
- Vertical zoom of the curves with Ctrl+mouse wheel.
- PDF manual installed with the program (`share/expo/docs/expo_manual.pdf`) and opened by Help > Documentation (PDF).

### Changed
- The background curve is shown while editing the background points.
- The bars of the experimental peaks are drawn on top of the background.
- The tools of the pattern toolbar are mutually exclusive, and clicking an active tool turns it off.
- `SCALE` directive of `%data`: the counts are rescaled to the range [0, scale], with the minimum count at 0 (previously only the maximum was set to scale).
- Simulated annealing: the cost function uses again the unweighted scale factor of the versions before 2.3.13.
- Intensity extraction: the background follows a steep background at the low-angle edge of the range.
- Help > Documentation (HTML), the Help buttons of the dialogs and the links of the About dialog open the new online documentation (https://iccnr-xtal.github.io/expo-docs/).
- Help > Check for Updates reads `updates.json` from the documentation site; the old address is no longer reachable.
- Help > Check for Updates on Windows now reads `updates.json`, as on Linux and macOS, instead of using the maintenance tool. Configure with `-DWINDOWS_IFW_UPDATER=ON` to go back to the old method.
- The Windows installer can now install over a previous version in the same folder: after confirmation it uninstalls that version first, instead of asking for a different folder.

### Fixed
- 3D viewer showing only the background and the axis labels on Wayland with recent NVIDIA drivers.
- `SCALE` directive of `%calculate_xpd` setting the FWHM instead of the scale.
- Explore Trials dialog: "Explore trials non processed yet" corrected to "not processed yet".

## [2.3.13] - 2026-10-26

### Added
- Context menu on the pattern plot area (right click) with Reset Zoom, Autoscale, Legend, Selection Mode, Horizontal/Rectangle Zoom, Pan and Plot Style.
- "Clear Selection" in the peak list menu to deselect all selected peaks.
- H–Cl distance added to the bond distances table.

### Changed
- Indexing: the peak position tolerance is fixed at 0.02° for Cu wavelengths and scaled from the Cu value for other wavelengths.
- Windows installer now shows the license page, skips the Start menu page and no longer creates an Uninstall shortcut.
- Fewer GUI event-processing calls during long Fortran computations, reducing overhead.
- License updated to GPLv3.

### Fixed
- N-TREOR failed to index patterns collected at short wavelengths (e.g. synchrotron radiation), although the same d-values were indexed with Cu radiation.
- Crash when reading pdCIF files whose profile is given as d-spacings (`_pd_proc_d_spacing`); the 2θ step is now computed correctly.
- In no-GUI mode the default wavelength is used when none is given in the data file.
- Crash when updating the background with no background points.
- Background not respecting the selected number of coefficients.
- Wavelength input validation in the wavelengths widget.
- Crash when the MPI build is started without `mpirun`, and settings lock contention between MPI processes at startup.
- GNOME: missing running indicator on the taskbar icon, and launch from the dash failing with Intel Fortran (ifx/ifort) builds.
- Invalid color/light settings could be read from the configuration file.
- Memory leak in the crystal structure viewer.
- Linux packaging: RPATH errors during Qt deployment.
- Linux: installing with `install.sh` (`sudo cmake --install`) put the desktop entry and icons in `/root/.local/share`, so expo had no icon in the dock and was not recognized by the desktop portal; they are now installed in `/usr/local/share` (in `~/.local/share` for a non-root install, and inside the package for DEB builds).
- Linux: "Failed to register with host portal" warning at startup with recent Qt versions.

## [2.3.12] - 2026-06-27

### Added
- New project dialog (`NewProjectDialog`) to create expo input files interactively, without editing them by hand.
- Short contacts can now be displayed with 3 different colors depending on distance range (short / mid / long), making it easier to spot the most relevant contacts at a glance.
- Docker image to run expo without installing it locally, supporting both the headless/MPI workflow and the graphical (X11) interface.
- Use extended selection mode for info table views

### Fixed
- Various minor bug fixes, including an indexing issue affecting the reflection index shown in the status bar.

Back to the [download page](download.md).
