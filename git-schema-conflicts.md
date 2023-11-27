## Migrations/schema conflicts

If your feature branch contains a migration, while another migration has been pushed to the main branch you will have to manually fix the conflict.

There is a very specific workflow for resolving conflicts in the `schema.rb` file.

First of all, make sure your migration(s) are at the bottom of the `migrate` directory. Just rename your files such that the timestamps (which is the first part of the filenames) are later than what is on the main branch.

Then, checkout the main branch and reset your database (this will probably result in data loss, good thing we have our seed file).

    git checkout main
    rails db:reset

Now you can start the rebase as usual.

    git checkout feature-a
    git rebase main

At first conflict, reset the schema to whatever is on the main branch.

    git checkout main db/schema.rb

Rebuild the schema, run your migrations and continue the rebase as described in an earlier section.

    rails db:migrate
    git rebase --continue

---

Relevant links:
 - https://paulgaumer.com/blog/resolving-a-schema-conflict-during-branch-rebase-in-rails
 - https://natemacinnes.github.io/how-to-handle-schema-conflicts-when-rebasing-git-rails.html
