<template>
    <nav class="navbar">
        <h1>Star Wars Portal</h1>
        <ul>
            <li><a href="#">Characters</a></li>
            <li><a href="#">Starships</a></li>
            <li><a href="#">Planets</a></li>
        </ul>
    </nav>
</template>

<style scoped>
.navbar {
    display: flex;
    justify-content: space-between;
    align-items: center;
    background-color: #111;
    padding: 1rem 2rem;
    color: white;
}

.navbar h1 {
    font-size: 1.5rem;
}

.navbar ul {
    list-style: none;
    display: flex;
    gap: 1.5rem;
}

.navbar a {
    color: white;
    text-decoration: none;
    font-weight: 500;
}

.navbar a:hover {
    color: #ffcc00;
}
</style>