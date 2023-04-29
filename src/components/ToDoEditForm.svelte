<script>
    import {TodoStore} from '../shops';
    import {Form, FormGroup, Input, Alert, Button} from 'sveltestrap';
    import {createEventDispatcher} from 'svelte';

    export let editTodo = null;
    let textErrorMessage = '';
    let validText = false;

    const dispatch = createEventDispatcher();

    const handleSubmit = event => {
        event.preventDefault();
        handleInput();
        if (validText) {
            const editedTodo = {
                ...editTodo,
                text: editTodo.text
            };
            TodoStore.update(currentTodos => {
                const updatedTodoIndex = currentTodos.findIndex(
                    t => t.id === editedTodo.id
                );
                const updatedTodos = [
                    ...currentTodos.slice(0, updatedTodoIndex),
                    editedTodo,
                    ...currentTodos.slice(updatedTodoIndex + 1)
                ];
                return updatedTodos;
            });
            dispatch('finish-edit');
        }
    };

    const handleInput = () => {
        validText = false;
        if (editTodo.text.length < 10) {
            textErrorMessage = "Todo text must be at least 10 characters";
        } else {
            textErrorMessage = '';
            validText = true;
        }
    };
</script>

<Form on:submit={handleInput}>
    <FormGroup floating label="Enter Todo Text">
        <Input type="text" bind:value={editTodo.text} />
    </FormGroup>
    {#if textErrorMessage.length > 0}
        <Alert color="danger">{textErrorMessage}</Alert>
    {/if}
    <Button type="submit" color="primary">Edit Todo</Button>
</Form>