# deepdiff

<https://zepworks.com/deepdiff/5.2.2/diff.html>

`pip install deepdiff`

## DeepDiff

`from deepdiff import DeepDiff`

- 参数

```text
ignore_order=True 忽略顺序，dict默认无序不需要加，但dict中如果有列表等有序类型想忽略顺序，则需要加
ignore_string_case 忽略大小写

exclude_paths=["root['c']['e']"] 排除指定字段的差异
exclude_types=[DeepDiff.numbers, DeepDiff.strings] 排除指定数据类型的差异，期望和实际中的都会被忽略

ignore_string_type_changes=True 忽略字符串类型间的差异，即认为string和bytes相同：比如“a”和b"a"，等同于ignore_type_in_groups=DeepDiff.strings
ignore_numeric_type_changes=True 忽略数字类型间的差异，即认为int和float相同：比如1和1.0，等同于ignore_type_in_groups=DeepDiff.numbers

verbose_level 输出等级
```

- 结果

```text
<class 'deepdiff.diff.DeepDiff'>

dictionary_item_added
dictionary_item_removed
iterable_item_removed
values_changed
type_changes

{
'dictionary_item_added': [root['c']], 
'dictionary_item_removed': [root['b']],
'values_changed': {"root['b']": {'new_value': 3, 'old_value': 2}}
'type_changes': {"root['b']": {'old_type': <class 'int'>, 'new_type': <class 'str'>, 'old_value': 2, 'new_value': '2'}}
}
```

- 示例

```python
from deepdiff import DeepDiff

old = {
    "a":11, 
    "b":22, 
    "c":{
        "d":4, 
        "e":[1,2,3]
        }
    }

new = {
    "a":11, 
    "b":22, 
    "c":{
        "d":44, 
        "e":[1,2,3]
        }
    }

# r = DeepDiff(old, new)
# r = DeepDiff(old, new, exclude_paths=["root['c']['e']"])
r = DeepDiff(old, new, exclude_types=[DeepDiff.numbers, DeepDiff.strings])
```
