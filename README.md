# imenu-list

This Emacs minor-mode creates an automatically updated buffer populated with the current buffer's imenu entries. The imenu-list buffer is typically shown as a sidebar (Emacs vertically splits the window).

Each source buffer gets its own independent imenu-list buffer named `*Ilist: <buffer-name>*`, so you can have simultaneous imenu sidebars for different files.

## Getting Started

To activate imenu-list for the current buffer, use `M-x imenu-list-minor-mode`.

You can also use `M-x imenu-list-smart-toggle` to toggle imenu-list on and off. It enables or disables the minor-mode depending on whether the imenu-list window is currently visible. You may wish to bind it to a key:

```elisp
(global-set-key (kbd "C-'") #'imenu-list-smart-toggle)
```

## Key Bindings

From the imenu-list buffer, you can use these shortcuts:

- `RET`: go to entry under cursor, or toggle folding for subtree entries
- `SPC`: display entry under cursor, but the imenu-list buffer remains current
- `mouse click`: same as `RET`
- `TAB`: expand/collapse subtree (`hs-toggle-hiding`)
- `f`: same as `TAB`
- `n`: next line
- `p`: previous line
- `g`: manually refresh entries
- `q`: quit window and disable `imenu-list-minor-mode`

## Configuration

### Focus After Activation

Some users might prefer the `imenu-list-minor-mode`/`imenu-list-smart-toggle` commands to also set the focus to the imenu-list window:

```elisp
(setq imenu-list-focus-after-activation t)
```

### Automatic Update

When `imenu-list-minor-mode` is enabled, the imenu-list buffer is updated automatically whenever the user is idle, with a default delay of `idle-update-delay` seconds. To change the delay time, customize `imenu-list-idle-update-delay`. To disable auto-update entirely:

```elisp
(setq imenu-list-auto-update nil)
```

### Auto-Resize

The imenu-list window can be automatically resized every time the buffer is updated:

```elisp
(setq imenu-list-auto-resize t)
```

Note that width resizing requires Emacs 24.4 or later due to a limitation in `fit-window-to-buffer`.

By default, the window width is capped at 30% of the frame width (`imenu-list-window-max-width` defaults to `0.3`). A float between 0 and 1 is treated as a fraction of the frame width; an integer specifies an absolute column count. To remove the cap:

```elisp
(setq imenu-list-window-max-width nil)
```

### After-Jump Hook

After jumping to an entry from the imenu-list buffer (e.g. via `RET` or `SPC`), the target buffer is recentered so the cursor is in the middle. To cancel that, reset `imenu-list-after-jump-hook`:

```elisp
(setq imenu-list-after-jump-hook nil)
```

### Close After Jump

By default, pressing `RET` on a leaf entry jumps to it while keeping the imenu-list window open. Subtree entries still toggle folding as usual. To automatically close the window after jumping:

```elisp
(setq imenu-list-close-after-jump t)
```

### Xref Marker Stack Integration

By default, imenu-list pushes a marker onto the xref marker stack before jumping. This allows `M-,` (`xref-go-back`) to return to the position before the jump. Requires Emacs 25.1 or later. To disable:

```elisp
(setq imenu-list-push-xref-marker nil)
```

### Entry Indicator

By default, imenu-list marks the current entry with a `>` character instead of `hl-line-mode`. The indicator is only shown when the imenu-list window is not the selected window. To switch back to `hl-line-mode` highlighting:

```elisp
(setq imenu-list-entry-indicator nil)
```

If you use `global-hl-line-mode` with the indicator enabled, consider excluding `imenu-list-major-mode`:

```elisp
(setq hl-line-global-modes '(not imenu-list-major-mode))
```

### Echo Truncated Entries

When the imenu-list window is narrow, long entry names may be truncated. By default, the full text of the current line is displayed in the echo area when it extends beyond the window width. Requires Emacs 25.1 or later. To disable:

```elisp
(setq imenu-list-echo-truncated-entry nil)
```

### Update Hook

It is possible to take further actions every time the imenu-list buffer is updated by using the hook `imenu-list-update-hook`.

## Display

imenu-list has several faces for showing different levels of nesting in the buffer. To customize them, see `M-x customize-group RET imenu-list RET`.

The mode-line of the imenu-list buffer can be changed by customizing `imenu-list-mode-line-format`, also available via `M-x customize-group RET imenu-list RET`.

Here are some pictures. Note that you can hide/show parts of the imenu list.

![](https://github.com/bmag/imenu-list/blob/master/images/imenu-list-light.png)

![](https://github.com/bmag/imenu-list/blob/master/images/imenu-list-dark.png)

## Window Position and Size

The size and position of the imenu-list window can be changed by customizing these variables:

- `imenu-list-position`: should be `left`, `right`, `above` or `below`, to display the window at the left, right, top or bottom of the frame.
- `imenu-list-size`: should be a positive integer or a percentage. If integer, decides the total number of rows/columns the window has. If percentage (0 < `imenu-list-size` < 1), decides the number of rows/columns relative to the total number of rows/columns in the frame.

imenu-list controls its display by adding an entry to `display-buffer-alist`. If you want fuller control over how the window is displayed, you should replace that entry.

If imenu-list can't open a new window (could happen when the frame is small or already split into many windows), the window will be displayed using the regular rules of `display-buffer`.

### window-purpose

For users of `window-purpose`, imenu-list adds an entry to `purpose-special-action-sequences`. If you want fuller control over how the window is displayed, you should replace that entry.
