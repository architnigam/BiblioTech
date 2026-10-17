# ER diagram

The SVG and PNG use Chen notation: entities are rectangles, attributes are ovals, primary keys are underlined, and relationships are diamonds. Relationship panels repeat the entities to avoid crossing lines. Each child references exactly one parent; parents have zero or many children, except a borrowing has zero or one fine. Both BOOK_AUTHORS attributes form its composite PK. No composite, multivalued or derived attribute symbols are used because this relational design does not contain those attribute types.

Edit er-diagram.svg for the Chen diagram. er-diagram.mmd retains the alternate Mermaid relational representation. Full foreign keys, domains and Oracle types are in ../data-dictionary.md.
