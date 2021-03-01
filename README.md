
# Batching and Subscriptions in DevDegree Store


## Requirements

**Make sure you have completed Code Along Milestone: GraphQL Part 2 first!**

## Batching

### Install Batch Gem
Add this line into your `Gemfile`
```ruby
gem 'graphql-batch'
```
Then dev up in your terminal
```
$ dev up
```

### Creating Record Loader
Create a fill in your graphql folder called `record_loader.rb` and paste this in
```ruby
class RecordLoader < GraphQL::Batch::Loader
  def initialize(model)
    @model = model
  end

  def perform(ids)
    @model.where(id: ids).each { |record| fulfill(record.id, record) }
    ids.each { |id| fulfill(id, nil) unless fulfilled?(id) }
  end
end
```

### Using Record Loader

Go to `app/graphql/schema.rb` and paste this in the `Schema` class
```ruby
use GraphQL::Batch
```

Next go to `query_type.rb` and make sure your orders method doesn't use eager loading

It should look like this
```ruby
def orders
  Order.all
end
```

Finally head to your `order_type.rb` file and add this method in
```ruby
def customer
  RecordLoader.for(Customer).load(object.customer_id)
end
```

And that should be it! You now have batching working in your store

Specifically this query

```graphql
query {
  orders {
    customers {
      id 
      firstName
      lastName
    }
  }
}
```

# Subscriptions

TODO
