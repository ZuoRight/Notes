# YAML

## list

```yaml
- v1
- v2
- v3
```

## dict

`{k:v}`

```yaml
k:v
```

```yaml
- k1:v1
- k2:v2
- k3:v3
```

## 嵌套

`[(1, 2),  (4, 7)]`

```yaml
-
 - 1
 - 2
-
 - 4
 - 7
```

`[{k:v}]`

```yaml
-
 k:v

```yaml
-
 - k1:v1
 - k2:v2
 - k3:v3
```

`yaml.safe_load(open("./xx.yaml"))`