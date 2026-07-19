# SPEC/design/theory

Magazine-style layout where images float alongside
text instead of breaking out of the container.

# Private

## Decisions

## Separate theme for theory entries

Theory entries share the same visual DNA as journal
entries (magazine-style floating images) but live in
their own theme so the two can diverge independently.

Considered: reusing the journal theme directly.
Discarded: the human wants theory to evolve its own
visual identity over time while starting from the
same base.

## Images float right at 40% width

Images float right and text wraps around them,
limited to 40% of the container width. On mobile
(below 768px), float is removed and images stack
in a single column.
