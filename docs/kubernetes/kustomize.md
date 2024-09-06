# [Kustomize]()

Kustomize works by having a default cofig and overwrite the default valus with overlays, this is pretty handy when working with multiple enviroments configs.

Kustomize is a native tool for building

It simplify the configuration files by making easy to use common values and generators.

Easly overwrites elements of your manifests file for each environment, by using patches and resources.

Generator automatically creates and attaches values to our pods, whithout needing to create the specific definition file. You can use it with ConfigMaps and Secrets.

It's also remove the learning curve as it works with default kubernetes resources config yaml files. Templating is complex and most of the times complexity it's not required.

Also enables the possibility injectiong config files in your apply.

It's in  builtin function in kubectl since version``v1.2.1``, just add ``-k/--kustomize`` to apply command, it's equivalent of ``kustomize | kunectl apply`` were it will generate the kubernetes definition files and appliy it.

> [!Tip]
> Verify installation: ``kubectl version``

Bases and Overlays works based on folder structure, were you will have your base config file and your overlays folders. It will copy the base folder and overwrite the defined overlays.

## Config Files

```bash
/
|- base /
|   |- kustomization.yaml
|   | # application default definition files .yaml
|- environments /
|   |- prd /
|       |- kustomization.yaml
|       | # overlay definition files
|       | # config files
```

When running the ``apply`` command it's required to point to the custom definiton files.

```bash
kubectl apply -k path/to/kustomize-folder/
```

The ``base/customize.yaml`` file is where you define the resources to be included, the common configs to be shared acrros folder resources and generators

```yaml
# kustomize.yaml
# base file
resources:
  # list definition files to be used
  - definition-file.yaml

commonLabels:
  # map common labels to be inject in resources
  # it's possible to remove pod definition for labels and selectors
  key: value

commonAnnotattions:
  key: value

namePrefix:

nameSufix:

configMapGenerator:
  - name:
    namespace:


```

The ``overlay/*/kustomization.yaml`` file will search for the base definitions and indicate wich custom definitons or patches should be applied upon default config.

```yaml
# kustomization.yaml
# overlay file
bases:
  # list base kistomization folder
  - path/to/base/folder/

patches:
  # list overlays files
  -

patchesStrategicMerge:
  - env.yaml
```

## Path Regex
