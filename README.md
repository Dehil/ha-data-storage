# Home Assistant Data Storage Sensor

This repository provides a template sensor for Home Assistant that implements a persistent key-value storage system using sensor attributes. It allows you to store and manage hierarchical data with up to 3 levels of nesting.

## Features

- Store hierarchical data with up to 3 levels of nesting
- Persistent storage using sensor attributes
- Event-driven updates
- Clean data management with add, remove, and clear operations

## Installation

1. Copy the `templates.yaml` file to your `config/` directory if not exist or add the content to your existing `templates.yaml`.
2. Add the template to your `configuration.yaml`:

```yaml
template: !include templates.yaml
```

## Usage

The sensor responds to three types of events:

1. `data_add`: Add data if not exist otherwise update existing data
2. `data_remove`: Removes data items from path with keys set in value
3. `data_clear`: Clear all data

### Event Data Structure

Events should contain the following data:
- `path`: List of keys defining the storage path (1-3 levels)
- `value`: The value to add (for `data_add` events) or keys to remove (for `data_remove` events)

## Examples

### Add Data

Add data
```yaml
- alias: Add data
  event: data_add
  event_data:
    path:
      - john_doe
    value:
      pre_name: John
      last_name: Doe
      age: 30
      address:
        shipping_address:
          street_address: Some Shipping Street
          city: Some Shipping City
```

Add item_01 to item_list:
```yaml
- alias: Add item_01 to item_list
  event: data_add
  event_data:
    path:
      - item_list
    value:
      item_01:
        item_id: 213412512
        item_name: First Item
        item_value: 12.99
```

Add item_01 - item_06:
```yaml
- alias: Add item_01 - item_06
  event: data_add
  event_data:
    path:
      - item_list
    value:
      item_01:
        item_id: 213412512
        item_name: First Item
        item_value: 12.99
      item_02:
        item_id: 152436712
        item_name: Second item
        item_value: 8.99
      item_03:
        item_id: 123516235
        item_name: Third item
        item_value: 4.99
      item_04:
        item_id: 132361134
        item_name: Fourth item
        item_value: 21.99
      item_05:
        item_id: 123516532
        item_name: Fourth item
        item_value: 6.99
      item_06:
        item_id: 123516532
        item_name: Fourth item
        item_value: 6.99
```

### Remove Data
Remove item_03 and item_04:
```yaml
- alias: Remove item_03 and item_04
  event: data_remove
  event_data:
    path:
      - item_list
    value:
      - item_03
      - item_04
```

### Update Data
Update item_05 and item_06:
```yaml
- alias: Update item_05 and item_06
  event: data_add
  event_data:
    path:
      - item_list
    value:
      item_05:
        item_id: 123516532
        item_name: Item 5
        item_value: 25.99
      item_06:
        item_id: 132316235
        item_name: Item 6
        item_value: 16.99
```

### Clear all data

```yaml
- alias: Clearing all data
  event: data_clear
  event_data: {}
```

## Accessing Stored Data

The data is stored in the sensor's `storage` attribute. You can access it in your automations or templates:

```yaml
{%- set storage = state_attr('sensor.data_storage', 'storage') %}
{{ storage.john_doe.pre_name }}
{{ storage.item_list.item_01.item_id }}
```

## Accessing Stored Data if Data exist

```yaml
{%- set item_01_exists = storage is mapping 
  and 'item_list' in storage
  and storage.item_list is mapping  
  and 'item_01' in storage.item_list
  and storage.item_list.item_01 is mapping
%}
{{ storage.item_list.item_01.item_name if item_01_exists else 'item_01 does not exist' }}
```

## Sensor State

- `timestamp`: Shows last time modified

## Attributes

- `storage`: Contains the actual data structure
- `last_changed`: ISO formatted timestamp of the last modification
- `friendly_name`: Sensor identifier

## Technical Details

The sensor maintains its state using the following logic:
- Updates state to `timestamp` when data is modified
- Add data if not exist otherwise update existing data
- Preserves data structure integrity during updates
- Handles invalid paths gracefully

## Limitations

- Maximum nesting depth of 3 levels
- Keys must be strings
- Values can be any valid YAML data type
- No array operations (use objects/dictionaries instead)

## Contributing

Feel free to submit issues and pull requests for improvements or bug fixes.

## License

MIT License - feel free to use and modify as needed.
