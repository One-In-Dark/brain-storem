According to Lua style guide, it's recommended that all fields in a table constructor be followed by a comma. And I'm tired of typing that all the time. Why don't we use tree-sitter to automate that?

```lua
local function in_multiline_table_constructor()
   local node = vim.treesitter.get_node()
   if node:type() ~= "table_constructor" then return false end
   local r1, _, r2 = node:range()
   return r1 ~= r2 -- multiline
end

vim.api.nvim_create_autocmd("FileType", {
   pattern = "lua",
   callback = function()
      vim.keymap.set('i', "=", function()
         return in_multiline_table_constructor() and "=,<Left>" or "="
      end, { expr = true, buffer = true })
      vim.keymap.set('i', "<BS>", function()
         -- get cursor position and check if it's preceding a comma and following a equal sign
         if not in_multiline_table_constructor() then return "<BS>" end
         local cursor_col = vim.api.nvim_win_get_cursor(0)[2]
         local line = vim.api.nvim_get_current_line()
         local prev_char = line:sub(cursor_col, cursor_col)
         local next_char = line:sub(cursor_col + 1, cursor_col + 1)
         return (prev_char == "=" and next_char == ",")
             and "<Right><BS><BS>" or "<BS>"
      end, { expr = true, buffer = true })
   end,
})
```
