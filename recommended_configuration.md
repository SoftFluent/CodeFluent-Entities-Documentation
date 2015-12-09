# Recommended configuration

The configuration we recommend is the following:
- Use local workspace (also recommended by Microsoft),
- Remove dates and diffs in generated files to avoid unneeded file checkout,
- Split your model into one part by type or one part by namespace if you are often adding or
removing entities,
- Check generated files in to the source server to get history and simplify the build process,
- Use different configuration to build the model with the continuous build process so you can
disable producers or change their configuration.