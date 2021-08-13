# ActiveRecord Alias Join

## Usage

```ruby
User.alias_joins(:post, alias: 'user_posts').where("user_posts.created_at > ?", 1.month.ago)
```
