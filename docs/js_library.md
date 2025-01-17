<!-- Generated with Stardoc: http://skydoc.bazel.build -->

js_library groups together JS sources and arranges them and their transitive and npm dependencies into a provided
`JsInfo`. There are no Bazel actions to run.

For example, this `BUILD` file groups a pair of `.js/.d.ts` files along with the `package.json`.
The latter is needed because it contains a `typings` key that allows downstream
users of this library to resolve the `one.d.ts` file.
The `main` key is another commonly used field in `package.json` which would require including it in the library.

```starlark
load("@aspect_rules_js//js:defs.bzl", "js_library")

js_library(
    name = "one",
    srcs = [
        "one.d.ts",
        "one.js",
        "package.json",
    ],
)
```

| This is similar to [`py_library`](https://docs.bazel.build/versions/main/be/python.html#py_library) which depends on
| Python sources and provides a `PyInfo`.

<a id="js_library"></a>

## js_library

<pre>
js_library(<a href="#js_library-name">name</a>, <a href="#js_library-deps">deps</a>, <a href="#js_library-srcs">srcs</a>, <a href="#js_library-data">data</a>, <a href="#js_library-copy_data_to_bin">copy_data_to_bin</a>, <a href="#js_library-declarations">declarations</a>, <a href="#js_library-no_copy_to_bin">no_copy_to_bin</a>)
</pre>

A library of JavaScript sources. Provides JsInfo, the primary provider used in rules_js
and derivative rule sets.

Declaration files are handled separately from sources since they are generally not needed at
runtime and build rules, such as ts_project, are optimal in their build graph if they only depend
on declarations from `deps` since these they don't need the JavaScript source files from deps to
typecheck.

Linked npm dependences are also handled separately from sources since not all rules require them and it
is optimal for these rules to not depend on them in the build graph.

NB: `js_library` copies all source files to the output tree before providing them in JsInfo. See
https://github.com/aspect-build/rules_js/tree/dbb5af0d2a9a2bb50e4cf4a96dbc582b27567155/docs#javascript
for more context on why we do this.

**ATTRIBUTES**


| Name  | Description | Type | Mandatory | Default |
| :------------- | :------------- | :------------- | :------------- | :------------- |
| <a id="js_library-name"></a>name |  A unique name for this target.   | <a href="https://bazel.build/concepts/labels#target-names">Name</a> | required |  |
| <a id="js_library-deps"></a>deps |  Dependencies of this target.<br><br>This may include other js_library targets or other targets that provide JsInfo<br><br>The transitive npm dependencies, transitive sources & runfiles of targets in the `deps` attribute are added to the runfiles of this target. They should appear in the '*.runfiles' area of any executable which is output by or has a runtime dependency on this target.<br><br>If this list contains linked npm packages, npm package store targets or other targets that provide `JsInfo`, `NpmPackageStoreInfo` providers are gathered from `JsInfo`. This is done directly from the `npm_package_store_deps` field of these. For linked npm package targets, the underlying `npm_package_store` target(s) that back the links is used. Gathered `NpmPackageStoreInfo` providers are propagated to the direct dependencies of downstream linked `npm_package` targets.<br><br>NB: Linked npm package targets that are "dev" dependencies do not forward their underlying `npm_package_store` target(s) through `npm_package_store_deps` and will therefore not be propagated to the direct dependencies of downstream linked `npm_package` targets. npm packages that come in from `npm_translate_lock` are considered "dev" dependencies if they are have `dev: true` set in the pnpm lock file. This should be all packages that are only listed as "devDependencies" in all `package.json` files within the pnpm workspace. This behavior is intentional to mimic how `devDependencies` work in published npm packages.   | <a href="https://bazel.build/concepts/labels">List of labels</a> | optional |  `[]`  |
| <a id="js_library-srcs"></a>srcs |  Source files that are included in this library.<br><br>This includes all your checked-in code and any generated source files.<br><br>The transitive npm dependencies, transitive sources & runfiles of targets in the `srcs` attribute are added to the runfiles of this target. They should appear in the '*.runfiles' area of any executable which is output by or has a runtime dependency on this target.<br><br>Source files that are JSON files, declaration files or directory artifacts will be automatically provided as "declarations" available to downstream rules for type checking. To explicitly provide source files as "declarations" available to downstream rules for type checking that do not match these criteria, move those files to the `declarations` attribute instead.   | <a href="https://bazel.build/concepts/labels">List of labels</a> | optional |  `[]`  |
| <a id="js_library-data"></a>data |  Runtime dependencies to include in binaries/tests that depend on this target.<br><br>The transitive npm dependencies, transitive sources, default outputs and runfiles of targets in the `data` attribute are added to the runfiles of this target. They should appear in the '*.runfiles' area of any executable which has a runtime dependency on this target.<br><br>If this list contains linked npm packages, npm package store targets or other targets that provide `JsInfo`, `NpmPackageStoreInfo` providers are gathered from `JsInfo`. This is done directly from the `npm_package_store_deps` field of these. For linked npm package targets, the underlying `npm_package_store` target(s) that back the links is used. Gathered `NpmPackageStoreInfo` providers are propagated to the direct dependencies of downstream linked `npm_package` targets.<br><br>NB: Linked npm package targets that are "dev" dependencies do not forward their underlying `npm_package_store` target(s) through `npm_package_store_deps` and will therefore not be propagated to the direct dependencies of downstream linked `npm_package` targets. npm packages that come in from `npm_translate_lock` are considered "dev" dependencies if they are have `dev: true` set in the pnpm lock file. This should be all packages that are only listed as "devDependencies" in all `package.json` files within the pnpm workspace. This behavior is intentional to mimic how `devDependencies` work in published npm packages.   | <a href="https://bazel.build/concepts/labels">List of labels</a> | optional |  `[]`  |
| <a id="js_library-copy_data_to_bin"></a>copy_data_to_bin |  When True, `data` files are copied to the Bazel output tree before being passed as inputs to runfiles.   | Boolean | optional |  `True`  |
| <a id="js_library-declarations"></a>declarations |  Same as `srcs` except all files are also provided as "declarations" available to downstream rules for type checking.<br><br>For example, a js_library with only `.js` files that are intended to be imported as `.js` files by downstream type checking rules such as `ts_project` would list those files in `declarations`:<br><br><pre><code>js_library(&#10;    name = "js_lib",&#10;    declarations = ["index.js"],&#10;)</code></pre>   | <a href="https://bazel.build/concepts/labels">List of labels</a> | optional |  `[]`  |
| <a id="js_library-no_copy_to_bin"></a>no_copy_to_bin |  List of files to not copy to the Bazel output tree when `copy_data_to_bin` is True.<br><br>This is useful for exceptional cases where a `copy_to_bin` is not possible or not suitable for an input file such as a file in an external repository. In most cases, this option is not needed. See `copy_data_to_bin` docstring for more info.   | <a href="https://bazel.build/concepts/labels">List of labels</a> | optional |  `[]`  |


<a id="js_library_lib.implementation"></a>

## js_library_lib.implementation

<pre>
js_library_lib.implementation(<a href="#js_library_lib.implementation-ctx">ctx</a>)
</pre>



**PARAMETERS**


| Name  | Description | Default Value |
| :------------- | :------------- | :------------- |
| <a id="js_library_lib.implementation-ctx"></a>ctx |  <p align="center"> - </p>   |  none |


