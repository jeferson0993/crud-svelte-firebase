<script>
    export let id
    export let description
    import { createEventDispatcher } from "svelte"
    const dispatcher = createEventDispatcher();
</script>

<tr>
    <td>{description}</td>
    <td on:click={() => dispatcher('edit', { id: id, description: description })}>
        <i class="fas fa-edit">
    </td>
    <td on:click={() => dispatcher('delete', { id: id })}>
        <i class="fas fa-trash">
    </td>
</tr>