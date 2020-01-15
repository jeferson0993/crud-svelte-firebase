<script>
    import { onMount } from "svelte"
    import TaskGridItem from "./TaskGridItem.svelte"
    let tasks = []
    let newDescription = ""
    let validDescription = false
    $: validDescription = newDescription.trim().length >= 5
    let id = null
    let url = "https://svelte-projects-3a9b3.firebaseio.com/tasks"
    let postEvent = false
    let patchEvent = false
    let deleteEvent = false
    const newTask = () => {
        id = null
        newDescription = ""
        document.querySelector("#newDescriptionId").focus()
    }
    const submitTask = () => {
        id ? updateTask() : insertTask()
    }
    const insertTask = () => {
        const taskData = { description: newDescription }
        fetch(url + ".json",{
            method: "POST",
            body: JSON.stringify(taskData),
            headers: { "Content-Type": "application/json" }
        }).then(response => {
            if (!response.ok)
                throw new error("Error!")
            return response.json()
        }).then(data => {
            tasks = [...tasks, { id: data.name, description: newDescription }]
            waitThreeSeconds("POST")
            newTask()
        }).catch(error => console.error(error))
    }
    onMount(() => {
        getTasks()
    })
    const getTasks = () => {
        fetch(url + ".json")
        .then(response => {
            if (!response.ok)
                throw new error("Error!")
            return response.json()
        }).then(data => {
            for (const key in data) {
                tasks = [...tasks, { id: key, description: data[key].description }]
                // console.table(tasks)
            }
        }).catch(error => console.error(error))
    }
    const updateTask = () => {
        const taskData = { description: newDescription }
        fetch(url + "/" + id + ".json",{
            method: "PATCH",
            body: JSON.stringify(taskData),
            headers: { "Content-Type": "application/json" }
        }).then(response => {
            if (!response.ok)
                throw new error("Error!")
            const index = tasks.findIndex(task => task.id === id)
            tasks[index] = { id: id, description: newDescription }
            waitThreeSeconds("PATCH")
            newTask()
        }).catch(error => console.error(error))
    }
    const editTask = (event) => {
        id = event.detail.id
        newDescription = event.detail.description
    }
    const deleteTask = (event) => {
        id = event.detail.id
        fetch(url + "/" + id + ".json",{
            method: "DELETE" 
        }).then(response => {
            if (!response.ok)
                throw new error("Error!")
            tasks = tasks.filter(task => task.id !== event.detail.id)
            waitThreeSeconds("DELETE")
            newTask()
        }).catch(error => console.error(error))
    }
    const waitThreeSeconds = (alert) => {
        switch (alert) {
            case "POST":
                postEvent = true
                break;            
            case "PATCH":
                patchEvent = true
                break;                
            default:
                deleteEvent = true
                break;
        }
        setInterval(function(){ 
            if (postEvent) {
                postEvent = !postEvent            
            } else if (patchEvent) {
                patchEvent = !patchEvent            
            } else if (deleteEvent) {
                deleteEvent = !deleteEvent            
            }
        }, 3000);
    }
</script>

<div class="container">
    <div class="notification">
        <div class="field is-horizontal">
            <div class="field-label is-normal">
                <label class="label">Tarefa</label>
            </div>
            <div class="field-body">
                <div class="field">
                    <p class="control is-expanded has-icons-left">
                       <input id="newDescriptionId" bind:value={newDescription} type="text" class="input" placeholder="Entre com uma nova tarefa..." /> 
                       <span class="icon is-small is-left">
                        <i class="fas fa-tasks"/>
                       </span>
                    </p>
                </div>
                <div class="field is-grouped">
                    <div class="control">
                        <button class="button is-danger" on:click={newTask}> Cancelar </button>
                    </div>
                    <div class="control">
                        <button class="button is-link" on:click={submitTask} disabled={!validDescription}>
                            <span class="icon is-small">
                                <i class="fas fa-check"/>
                            </span>
                            <span>Salvar</span>
                        </button>
                    </div>
                </div>
            </div>
        </div>
        <div class="field is-horizontal adding-record">
            <div class="fild-label">Nova tarefa: </div>
            <div class="field-body">
                <div class="field">
                    <div class="control"> {newDescription}</div>
                </div>
            </div>
        </div>
        {#if postEvent}             
            <div class="_200 notification is-info">
                <button class="delete"></button>
                <strong>Inserido</strong>.
            </div>
        {/if}
        {#if patchEvent}
            <div class="_200 notification is-warning">
                <button class="delete"></button>
                <strong>Atualizado</strong>.
            </div>
        {/if}
        {#if deleteEvent}
            <div class="_200 notification is-danger">
                <button class="delete"></button>
                <strong>Excluido</strong>.
            </div>
        {/if}
    </div>
</div>

<div class="container">
    <h4 class="title is-4">
        <table class="table is-bordered is-striped is-hoverable">
            <thead>
                <th>Descrição</th>
                <th>Editar</th>
                <th>Excluir</th>
            </thead>
            <tbody>
                {#each tasks as task}
                    <TaskGridItem 
                    id={task.id} 
                    description={task.description} 
                    on:edit={editTask}
                    on:delete={deleteTask} />
                {:else}
                    <tr>
                        <td>Não há tarefas <i class="fas fa-sad-tear"/></td>
                        <td/>
                        <td/>
                    </tr>
                {/each}
            </tbody>
        </table>
    </h4>
</div>

<style>
h4 {
    margin-top: 1em;
}
.adding-record {
    color: darkblue;
}
.is-4 {
    display: flex;
    align-content: center;
    justify-content: center;
}
._200 {
    width: 200px;
    margin: auto;
}
</style>
