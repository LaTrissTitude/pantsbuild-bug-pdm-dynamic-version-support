# Bug : Pants missing support for PDM backend dynamic version from git tags ?

## Steps to Reproduce

```sh
# Clone this repo
git clone git@github.com:LaTrissTitude/pantsbuild-bug-pdm-dynamic-version-support.git
cd pantsbuild-bug-pdm-dynamic-version-support

# Add version tag
git tag project/1.2.3 -m "Sample tag"

# Works as expected
# Produces dist/project-1.2.3+...-py3-none-any.whl
pdm build -p src/project -d ../../dist

# Fails, PDM backend cannot find the required SCM tags
# Should produce dist/project-1.2.3+...-py3-none-any.whl
pants package ::
```

## Expected

Compiling through PDM backend using PDM via `pdm build` should be able to generate the wheel `dist/project-1.0.0-py3-none-any.whl`.

Compiling through PDM backend using Pants via `pants package ::`  should also be able to generate the wheel `dist/project.1.0.0-py3-none-any.whl`.

## Actual

Compiling through PDM backend using PDM via `pdm build` generates the wheel `dist/project-1.0.0-py3-none-any.whl`.

Compiling through PDM backend using Pants via `pants package ::` fails with the following error: `ValueError: Cannot find the version from SCM or SCM isn't detected.` (see below for full traceback).



### Full Traceback
```sh
$ pants package ::

16:49:56.87 [ERROR] 1 Exception encountered:

Engine traceback:
  in `package` goal

ProcessExecutionFailure: Process 'Run pdm.backend for src/project:dist' failed with exit code 1.
stdout:

stderr:
Traceback (most recent call last):
  File "/tmp/pants-sandbox-ckr5HR/chroot/src/project/../../../.cache/pex_root/venvs/39b8f0fe2bb93037b00f78bff533ef34fecaa5b6/5985ed09b49a653d6596b0e14d134c5456cf1a9f/pex", line 263, in <module>
    exec(ast, globals_map, locals_map)
  File "backend_shim.py", line 25, in <module>
    wheel_path = backend.build_wheel(dist_dir, wheel_config_settings) if build_wheel else None
  File "/home/latrisstitude/.cache/pants/named_caches/pex_root/venvs/s/28aea897/venv/lib/python3.10/site-packages/pdm/backend/__init__.py", line 54, in build_wheel
    return builder.build(
  File "/home/latrisstitude/.cache/pants/named_caches/pex_root/venvs/s/28aea897/venv/lib/python3.10/site-packages/pdm/backend/base.py", line 213, in build
    self.initialize(context)
  File "/home/latrisstitude/.cache/pants/named_caches/pex_root/venvs/s/28aea897/venv/lib/python3.10/site-packages/pdm/backend/base.py", line 180, in initialize
    self.call_hook("pdm_build_initialize", context)
  File "/home/latrisstitude/.cache/pants/named_caches/pex_root/venvs/s/28aea897/venv/lib/python3.10/site-packages/pdm/backend/base.py", line 138, in call_hook
    getattr(hook, hook_name)(context, *args, **kwargs)
  File "/home/latrisstitude/.cache/pants/named_caches/pex_root/venvs/s/28aea897/venv/lib/python3.10/site-packages/pdm/backend/hooks/version/__init__.py", line 69, in pdm_build_initialize
    metadata["version"] = getattr(self, f"resolve_version_from_{source}")(
  File "/home/latrisstitude/.cache/pants/named_caches/pex_root/venvs/s/28aea897/venv/lib/python3.10/site-packages/pdm/backend/hooks/version/__init__.py", line 98, in resolve_version_from_scm
    version = get_version_from_scm(context.root, tag_regex=tag_regex)
  File "/home/latrisstitude/.cache/pants/named_caches/pex_root/venvs/s/28aea897/venv/lib/python3.10/site-packages/pdm/backend/hooks/version/scm.py", line 336, in get_version_from_scm
    raise ValueError(
ValueError: Cannot find the version from SCM or SCM isn't detected.
You can still specify the version via environment variable `PDM_BUILD_SCM_VERSION`.



Use `--keep-sandboxes=on_failure` to preserve the process chroot for inspection.
```
