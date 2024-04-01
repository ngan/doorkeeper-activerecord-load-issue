This is a sample app to reproduce an issue with the doorkeeper gem loading ActiveRecord too early.

## Preface

This repo is a fresh Rails 7.1 app with no application code. I've vendored the activerecord gem into `vendor/activerecord-7.1.3.2` to add some `puts`.

## Normal behavior

To see the normal behavior (without `doorkeeper` installed):

1. Remove `gem "doorkeeper"` from the `Gemfile`.
2. Run `bundle install`.
3. Run `rails runner "true"`.

You should see something:

```
------- (in initialize)
configs: ActiveSupport::OrderedOptions (object_id: 10320)"
------- (in after_initialize)
configs: ActiveSupport::OrderedOptions object_id: (10320)"
```

## Problematic behavior

To see the problematic behavior (with `doorkeeper` installed):

1. Add `gem "doorkeeper"` to the `Gemfile`.
2. Run `bundle install`.
3. Run `rails runner "true"`.

You should see something like:

```
------- (in initialize)
configs: ActiveSupport::OrderedOptions (object_id: 10880)"
------- (in after_initialize)
configs: Hash object_id: (15100)"

```
## Breakdown

When `doorkeeper` is installed, the `configs` variable is somehow changed from a `ActiveSupport::OrderedOptions` to a `Hash` object. This means that configurations that are set inside the app's intializer are discarded.
