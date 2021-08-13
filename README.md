# ActiveRecord Alias Join

## Usage

```ruby
User.alias_joins(:post, alias: 'user_posts').where("user_posts.created_at > ?", 1.month.ago)
```


## Code

TODO: Look into switching to Arel syntax to support through relationships through relationship support, https://groups.google.com/g/rails-oceania/c/iHlJPSd-lKM

```ruby
def alias_joins(assoc_name, alias:){
  assoc_reflection = self.klass.reflect_on_all_associations.detect{|x| x.name.to_s == assoc_name.to_s}
  
  if assoc_reflection.nil?
    raise ArgumentError.new("Association '#{assoc_name}' not found on class '#{self.klass.name}'")
  elsif assoc_reflection.through_reflection
    raise ArgumentError.new(":through relationships are not current supported. PR Wanted.")
  end

  if assoc_reflection.belongs_to?
    join_on = "ON #{self.connection.quote_table_name(self.klass.table_name)}.#{self.connection.quote_column_name(assoc_reflection.foreign_key)} = #{self.connection.quote_table_name(assoc_name)}.#{self.connection.quote_column_name(assoc_reflection.association_primary_key)}"
  else
    join_on = "ON #{self.connection.quote_table_name(self.klass.table_name)}.#{self.connection.quote_column_name(assoc_reflection.association_primary_key)} = #{self.connection.quote_table_name(assoc_name)}.#{self.connection.quote_column_name(assoc_reflection.foreign_key)}"
  end

  sql_join_str = "LEFT OUTER JOIN #{self.connection.quote_table_name(alias)} AS #{self.connection.quote_table_name(assoc_reflection.name)} #{join_on}"
  
  return self.joins(sql_join_str)
end
```
