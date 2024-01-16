# Remove a Column
> See https://api.rubyonrails.org/classes/ActiveRecord/ModelSchema/ClassMethods.html#method-i-ignored_columns-3D

### Steps:
1. Make sure column has default value or is nullable.
    <details>
    <summary>Skip if column is nullable.</summary>

    ```ruby
    class ChangeDefaultNameForUsers < ActiveRecord::Migration[7.3]
      def change
        change_column_default :users, :name, from: nil, to: ""
      end
    end
    ```
    Deploy and migrate!

    </details>

2.  Ignore the column
    ```ruby
    class User < ApplicationRecord
      self.ignored_columns << :name
    end
    ```
3.  Deploy!
4.  Write a migration to remove the column
    ```ruby
    class RemoveNameFromUsers < ActiveRecord::Migration[7.3]
      def change
        remove_column :users, :name
      end
    end
    ```
5.  Deploy and migrate!
6.  Remove the `ignored_columns` line from previous step.
