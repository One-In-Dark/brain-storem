According to Lua style guide, it's recommended that all fields in a table constructor be followed by a comma. And I'm tired of typing that all the time. Why don't we use tree-sitter to automate that?

```lua
vim.keymap.set('i', "=", function()
   local node = vim.treesitter.get_node()
   if node:type() ~= "table_constructor" then
      return "="
   end
   local r1, _, r2 = node:range()
   return r1 ~= r2 and "=,<Left>" or "="
end, { expr = true })
```
