# Event and Event Handler in Razor Component

## onclick event

```html
<tbody>
  @foreach (Party party in Parties) {
  <tr @onclick="() => SelectedParty = party" style="cursor: pointer;">
    <td>@party.Details?.Type</td>
    <td>@GetFullName(party)</td>
  </tr>
  }
</tbody>
```
