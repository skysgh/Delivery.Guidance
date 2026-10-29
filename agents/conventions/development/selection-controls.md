# Selection Controls

Use this convention whenever a user interface asks a person to choose a value from a list, whether in a form, an
inspector, a designer or a settings page.

## Rule

**Do not use a drop-down, `<select>`, combobox or equivalent list box to choose from a list.** A drop-down shows a
bare label, cannot carry an icon, a description or a preview, cannot be searched or paged, and cannot grow. It is
the wrong default even when the list is short today, because the list is rarely short tomorrow.

Choose through a **panel-based picker** instead:

1. The field renders the current value as a read-only, visibly styled summary (icon and title) plus a hidden value
   that holds the identifier. It is not free text; a typo cannot be introduced.
2. Activating the summary opens the side panel.
3. The panel presents the candidates through a presenter over the universal display-item contract: each candidate
   has an identifier, a title, an optional icon and an optional description. Search, filters and paging are added to
   the panel as the list needs them.
4. Choosing returns the identifier and the title. Dismissing the panel leaves the current value unchanged.
5. A value that is no longer in the list is shown as stored and preserved, not silently replaced.

## The candidates come from a registered data source

The panel never owns the list. The list is a registered source resolved by key through the one broker registry
(see [Named data access](./named-data-access.md)), so the same list can feed a picker, a browse page and published
definitions. Search and paging are sent to the source, which answers them whether it is local or remote.

- **A source already exists:** reuse it by key.
- **No source exists: create one.** This is the expected path, not a reason to fall back to a drop-down.
- **The list is small, static or a closed vocabulary** (for example heading levels H1-H3 and what each is for, or
  the display styles a presenter supports): build the source on the client, register it under a versioned key and
  give each item a title and a description that explains its purpose.
- **The list needs the backend** (persisted, tenant- or permission-scoped, large, or changing at runtime): stop and
  wire it through the whole vertical slice to the server's data store and its governed query surface before the
  picker is built. Do not substitute a hard-coded client list for data that must be authoritative, and do not leave
  a drop-down behind as a placeholder.

## Do not claim what you have not verified

When asked to source a choice from the data agent, confirm the registered source and the panel presenter exist and
are used before saying so. A template that renders a `<select>` is not a data-agent-backed picker, and reporting it
as one is a defect in the report as much as in the code.

## Narrow exceptions

A native boolean toggle, a small segmented toggle of two or three mutually exclusive modes whose meanings are
self-evident, and a browser-native input that is the point of the control (date, colour) are not list choices. If
you believe another control is an exception, record the reason in the change; do not assume it.

## Review checks

- No new `<select>`, combobox or list box exists for a list choice.
- Each choice has a registered source key, items with a title and an icon or description where meaningful, and a
  test showing the id and title are returned and dismissal changes nothing.
- A newly required backend list was built end to end, or the work was paused and the gap reported.

[Up: Development Conventions](./readme.md)
