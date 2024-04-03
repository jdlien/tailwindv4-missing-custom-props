# tailwindv4-missing-custom-props

## Demo: Missing props used without fallback value breaking in Firefox Stable v124

In tailwindcss 4.0.0-alpha.11, several styles are not applied in current stable builds of Firefox (v124). The ones I've noticed thus far are:

- space-x utilities
- border utilities

This is because Firefox v124 does not yet support the @property rule, and thus several CSS custom properties are undefined.

Consequently, styles like space-x-_ and border-_ are not applied in Firefox v124.

The properties are set by Tailwind v4 like using @property rule like:

```css
@property --tw-space-x-reverse {
  syntax: "<number>";
  inherits: false;
  initial-value: 0;
}
```

This currently results in code like the following that uses the undefined custom props without a fallback value:

```css
.space-x-8 {
  :where(& > :not([hidden]) ~ :not([hidden])) {
    margin-inline-end: calc(var(--spacing-8, 2rem) * var(--tw-space-x-reverse));
    margin-inline-start: calc(var(--spacing-8, 2rem) * calc(1 - var(--tw-space-x-reverse)));
  }
}
```

The most straightforward solution is to add the appropriate fallback values to the custom properties where they are used. For example, the above code could be modified to:

```css
.space-x-8 {
  :where(& > :not([hidden]) ~ :not([hidden])) {
    margin-inline-end: calc(var(--spacing-8, 2rem) * var(--tw-space-x-reverse, 0));
    margin-inline-start: calc(var(--spacing-8, 2rem) * calc(1 - var(--tw-space-x-reverse, 0)));
  }
}
```

See also cases where the `--tw-border-style` is used. There should also be a fallback to `solid`:

Current:

```
  .border {
    border-style: var(--tw-border-style);
    border-width: 1px;
  }
```

Suggested:

```
  .border {
    border-style: var(--tw-border-style, solid);
    border-width: 1px;
  }
```

This demo can be viewed at:
https://jdlien.com/tailwindv4-missing-custom-props/
