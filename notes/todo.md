# TODO — bugs found in code read-through (2026-10-16)

1. **Scale select breaks zoom** — [quick-tools.js:55](../js/quick-tools.js#L55) `setScaleIndex(event.target.value)` stores a string; later wheel zoom does `"3" + 1 = "31"` → `scales["31"]` is `undefined`.
2. **Undo history wiped** — any edit after an undo clears the entire history ([current-layout.js:37-40](../js/current-layout.js#L37-L40)) instead of truncating to `historyIndex + 1`.
3. **Move + Escape deletes the component** — [move-component.js](../js/move-component.js) removes it from the layout (and commits to history) before the ghost is placed.
4. **Falsy layout values lost** — `getCurrentLayout(key)` returns `undefined` for `perf: false` / `0`. Loading a layout sets the perf checkbox's `.value` instead of `.checked`.
5. **Shared option objects mutated** — resistor, diode, inductor, LED, wire, potentiometer `getOptions` mutate the module-level defaults instead of a deep copy (e.g. 1/4W resistor keeps pitch default 6 after selecting 1/2W). Resistor 1/2W pitch list contains `10` twice.
6. **Capacitor issues** — Radial uses `if(component.polarised)` but the value is the string `"Non-polarised"` (truthy) → always shows "+". "Axial" option has no draw branch → empty group, hole detector crashes on `legs[0]`.
7. **Diode BOM value always "-"** — `getDiodeBOMLine` reads `component.value`; the field is `partNumber`.
8. **Placement with no highlighted hole** — `getSelectedHoles()[0]` crashes, or reuses a stale hole from the previous placement (selected holes are never cleared).
9. ~~**Minor**~~ ✅ fixed 2026-10-16
   - ~~Help dialog says Shift+C to duplicate; code uses Ctrl+C.~~ (help text now says Ctrl+C)
   - ~~TO-92/TO-39 alias typo `"tranistor"` — searching "transistor" finds nothing.~~
   - ~~[index.html:90](../index.html#L90) GitHub link closes with `<a>` instead of `</a>`.~~
   - ~~[current-layout.js:84](../js/current-layout.js#L84) closing `</span>` is dead code after a semicolon.~~
10. ~~**Sidebar buttons unreachable for large components** (e.g. 18-way DIP)~~ ✅ fixed 2026-10-17 — the window wheel handler in `controls.js` blocked sidebar scrolling (it zoomed instead), and the sidebar (100vh + padding) ran underneath the sticky footer, which covered its bottom. Wheel zoom now only applies over the canvas. The body is a flex column so the sidebar ends at the footer, and `calculateWorkCentre` no longer subtracts sidebar/footer sizes (the canvas used to be oversized).
