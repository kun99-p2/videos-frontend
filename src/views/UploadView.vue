<template>
    <h1 style="display: flex; justify-content: center;">Upload</h1>
    <div class="container">
        <div class="thumbnail">
        </div>
        <div class="details">
            <div class="input">
                <label for="title">Title:</label>
                <input type="text" placeholder="Enter video title">
            </div>
            <div class="input">
                <label for="description">Description:</label>
                <textarea rows="9" placeholder="Enter video description"></textarea>
            </div>
            <div class="buttons">
                <RouterLink to="/">
                    <button>Cancel</button>
                </RouterLink>
                <button>OK</button>
            </div>
        </div>
    </div>
</template>

<style>
.container {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    padding-left: 300px;
}

.thumbnail {
    width: 400px;
    height: 300px;
    object-fit: cover;
    border: 1px solid #ccc;
}

.details {
    flex: 1;
    margin-left: 20px;
}

.input{
    margin-bottom: 20px;
    width: 600px;
}

.input label {
    display: block;
    font-weight: bold;
    margin-bottom: 5px;
}

.input input[type="text"],
.input textarea {
    width: 100%;
    padding: 8px;
    font-size: 16px;
    border: 1px solid #ccc;
}

.buttons {
    display: flex;
    padding-left: 500px;
    justify-content: space-between;
    width: 100px;
}

buttons button {
    margin-left: 10px;
    padding: 10px 20px;
    font-size: 16px;
    cursor: pointer;
}
</style>

<script>
</script>