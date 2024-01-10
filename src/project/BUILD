python_sources(name="lib")

# Defines the package name, version, metadata and source files
resource(name="pyproject", source="pyproject.toml")

python_distribution(
    name="dist",
    dependencies=[":lib", ":pyproject"],
    provides=python_artifact(name="artifact"),
    generate_setup=False,
)
