# The Problem

I wanted to inspect the styling of a dropdown with Chrome Dev Tools. But when I activate the function `select an element to inspect`, the dropdown closes its menu due to `blur` event.

# Solution 1

There are some solutions I found in Internet. But this one is quite easy to use and doesn't require to write code.

- Inspect the dropdown
- Select the Input element of the dropdown
- Go to Event listeners and remove the blur event handler
- Now the dropdown menu will not close if you lost focus of the control

# Solution 2

If you are working with a custom dropdown, you may need this second solution.

- In Dev Tools, go to the parent element of the dropdown.
- Right click on the parent element and choose `Break on` - `subtree modifications`.
- Now, if you click the dropdown, one child element should change and the debugger stops the process. The menu is freezing.
- You can now inspect the menu.