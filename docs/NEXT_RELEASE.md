# Next release — collecting

Changes asked for after v0.57 and not yet built. Tomas is collecting; nothing here is started.
Each item says what was asked, what it touches, and what somebody picking it up needs to know that
is not obvious from the code.

Add to the bottom. When a release goes out, move what shipped into its tag message and delete it
from here, so this file is only ever what is still owed.

---

## 1. The dial belongs on Now, and nowhere else

**Asked, 16 September 2026, after v0.57 on node #1.** The scale/resolution bar is drawn on every
view. On Now it is the control the page is built around. On Network, Historical, Set up and Arrange
it is redundant and distracting — nothing on those views changes when it turns.

**Where it is drawn.** `main()` in `app/static/dashboard.js`:

```js
const head = () => chrome(S.health.node, S.health.city, VIEW)
  + `<div class="dialwrap"><div class="wrap">${dial()}</div></div>`;
```

and `head()` is called by the `now`, `network`, `historical`, `setup`/`arrange` and `refused`
branches alike. The wall has its own separate dial (`WALL.dial`, `#wall-dial`), which is a different
control on a different surface and is NOT in scope: it re-fills the wall's field as it turns, which
is the whole point of a wall.

**What it touches, checked rather than assumed.** Nine elements carry `data-ref="dial"`:

    ground-figure (x2)   cellGroup   claim   grain-table   flat-run (x2)   grain-line (x2)

Every one of them is on Now. So removing the dial from the other views orphans nothing and T5 — every
component links in or out — stays satisfied. This is the check to re-run after the change, not to
trust from this note.

**The one thing to decide while doing it.** `dial()` picks its own link-out by view:

```js
const ref = VIEW === 'now' ? 'grain-line' : VIEW === 'network' ? 'satellite' : `view-${VIEW}`;
```

The `network` arm points at `satellite`, which moved to Historical in v0.56 and is no longer on
Network at all — so that arm is already pointing at an id that is not on the page. Removing the dial
from those views deletes the problem rather than fixing it, which is fine, but it should be noticed
and not left behind if the dial ever comes back to another view.

**Also worth knowing.** The resolution still lives in the URL (`?res=`), so a link into Historical
or Network at a given grain keeps that grain — the reader just cannot change it from there. That is
the intended behaviour, not a gap: those views do not vary by grain.

**Cost if wrong:** a reader on Network who wanted to change grain has to go back to Now first. Small,
and reversible in one line.

---
