Custom chef recipes need to be modified for stable-v5.

1. Chef has been upgraded to Chef 12. You must add `metadata.rb` to your recipes. Add `name "env_motd"`.

      Check out [env_motd/metadata.rb](https://github.com/crigor/sample_v5_env_motd/tree/master/cookbooks/env_motd/metadata.rb).

2. The dna.json format has changed. `instance_role`, `name`, and other data were moved from the "root" to `dna{}`.

      Check out [env_motd/recipes/bash.rb](https://github.com/crigor/sample_v5_env_motd/blob/master/cookbooks/env_motd/recipes/bash.rb#L36-L38).

3. `ey-custom::after-main` is now the entrypoint of your custom chef recipes. Add `include_recipe "env_motd"` on `ey-custom/recipes/after-main.rb` and `depends "env_motd"` on `ey-custom/metadata.rb`.

      Check out [after-main.rb](https://github.com/crigor/sample_v5_env_motd/tree/master/cookbooks/ey-custom/recipes/after-main.rb) and [ey-custom/metadata.rb](https://github.com/crigor/sample_v5_env_motd/tree/master/cookbooks/ey-custom/metadata.rb).

4. The main chef recipes and custom chef recipes are combined and used in a *single* chef run. On stable-v4, there are 2 seperate chef runs. If your custom chef recipes do not change the behavior of our main chef recipes nor change the files created by our main recipes, you don't have to modify your recipes related to this change.

Here's the diff between env_motd v4 and v5 recipes.

```diff
$ diff -r env_motd_v4 env_motd_v5
diff -r env_motd_v4/metadata.rb env_motd_v5/metadata.rb
0a1
> name 'env_motd'
diff -r env_motd_v4/recipes/bash.rb env_motd_v5/recipes/bash.rb
36,38c36,38
<     :name => node[:name],
<     :role => node[:instance_role],
<     :env_name => node[:environment][:name],
---
>     :name => node[:dna][:name],
>     :role => node[:dna][:instance_role],
>     :env_name => node[:dna][:environment][:name],
diff -r env_motd_v4/recipes/motd.rb env_motd_v5/recipes/motd.rb
32,34c32,34
<     :name => node[:name],
<     :role => node[:instance_role],
<     :env_name => node[:environment][:name],
---
>     :name => node[:dna][:name],
>     :role => node[:dna][:instance_role],
>     :env_name => node[:dna][:environment][:name],
```
