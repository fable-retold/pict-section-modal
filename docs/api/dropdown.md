# dropdown

Open an anchor-positioned dropdown menu or free-form popover. Unlike a modal window there is **no backdrop** — the rest of the page stays interactive. The element is positioned next to its anchor, auto-flips above when there isn't room below, clamps inside the viewport, and dismisses on click-outside or `Escape` (native menu conventions).

Two content modes:

- **Menu mode** — pass `items` and the helper builds an accessible `role="menu"` list with keyboard navigation, returning the selection.
- **Content mode** — pass `ContentHTML` and the helper renders that HTML verbatim as a plain anchored popover (a rich card, or a pre-rendered template menu), keeping the same dismiss / flip / reposition behavior.

## Signature

```javascript
modal.dropdown(pAnchor, pOptions)
```

## Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| pAnchor | HTMLElement \| string \| object | Yes | The anchor: a DOM element, a CSS selector, or a rect-like `{ left, top, width, height }` (handy for context menus) |
| pOptions | object | Yes | Configuration (see below) |

### Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| items | Array | `[]` | Menu items: `[{ Hash, Label, Style?, Disabled?, Tooltip?, Icon?, Hint?, Separator?, Header? }]`. Ignored when `ContentHTML` is set |
| ContentHTML | string | — | Free-form HTML body rendered verbatim via `innerHTML` **instead of** building a menu from `items`. The element becomes a plain anchored popover (no `role="menu"` / keyboard item nav). Sanitize untrusted content |
| align | string | `"left"` | Horizontal alignment to the anchor: `"left"`, `"right"`, or `"center"` |
| position | string | `"auto"` | Vertical placement: `"auto"`, `"below"`, or `"above"` |
| minWidth | string | anchor width, else `"160px"` | CSS min-width |
| maxWidth | string | — | CSS max-width. The base menu caps at 360px; the content variant uncaps — set this for wide rich-content popovers |
| maxHeight | string | `"60vh"` | CSS max-height (the menu scrolls internally past this) |
| className | string | — | Extra class(es) on the element. Use it to theme a specific popover by overriding the `--pict-modal-dropdown-*` custom properties |
| closeOnSelect | boolean | `true` | Menu mode only — dismiss on item selection |
| onSelect | function | — | Menu mode only — called with `(Hash, Item)` on selection |
| onClose | function | — | Called after dismiss, with the resolved result |

## Returns

`Promise<{ Hash: string, Item: object } | null>` — resolves with the selected item in menu mode, or `null` on dismiss. **Always resolves `null` in `ContentHTML` mode** (there is no built-in selection; wire actions inside your HTML).

## Examples

### Menu

```javascript
modal.dropdown(document.querySelector('#row-menu-button'),
	{
		align: 'right',
		items:
		[
			{ Hash: 'edit',   Label: 'Edit'                      },
			{ Hash: 'rename', Label: 'Rename...'                 },
			{ Separator: true                                    },
			{ Hash: 'delete', Label: 'Delete', Style: 'danger'   }
		]
	}).then((pChoice) =>
	{
		if (pChoice && pChoice.Hash === 'delete') { /* ... */ }
	});
```

### Free-form content popover

```javascript
// Render a template to HTML elsewhere, then drop it next to its anchor.
let tmpHTML = pict.parseTemplateByHash('Quick-Summary-Template', tmpRecord);
modal.dropdown(document.getElementById(pAnchorId),
	{
		ContentHTML: tmpHTML,
		maxWidth: '800px',
		align: 'left'
	});

// Anything clickable inside tmpHTML drives its own behavior; call
// modal.dismissDropdowns() from an action handler to close the popover.
```

## Notes

- Only one dropdown is open at a time; opening a new one dismisses the current.
- Re-opening the dropdown for the same anchor returns the existing open Promise (no flicker).
- `ContentHTML` mode adds a `pict-modal-dropdown--content` modifier that uncaps the width and removes the menu's internal padding, so the injected content controls its own sizing and spacing.
- Dismiss everything with [`dismissDropdowns()`](dismissAll.md) (or `dismissAll()`).

## See also

- [tooltip](tooltip.md) / [richTooltip](richTooltip.md) — hover/focus tooltips
- [show](show.md) — modal window with a backdrop
