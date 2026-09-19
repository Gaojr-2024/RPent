# Conditional exposure flow

## Operation

1. Dashboard filters runtime components through `enabled_if_args`.
2. `LiberoToolkit` registers `wam_act` only when primitives received
   `wam_model`.
3. `RunConfig.prompt_vars["wam_enabled"]` controls the optional prompt section.
4. With no WAM flags, component startup, prompt text, and available tool names
   remain unchanged.

## Evidence

Toolkit, config, prompt, and explicit tool-schema contracts pass in the focused
test set.
