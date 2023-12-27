<template>
  <div>
    <nav
      class="navbar navbar-expand-lg navbar-light bg-light"
      style="padding: 1em"
    >
      <router-link class="navbar-brand" :to="{ name: 'Home' }"
        >Todo Lsit</router-link
      >
      <button
        class="navbar-toggler"
        type="button"
        data-toggle="collapse"
        data-target="#navbarSupportedContent"
        aria-controls="navbarSupportedContent"
        aria-expanded="false"
        aria-label="Toggle navigation"
      >
        <span class="navbar-toggler-icon"></span>
      </button>

      <div
        class="collapse navbar-collapse"
        id="navbarSupportedContent"
        style="justify-content: space-between"
      >
        <ul class="navbar-nav mr-auto">
          <li class="nav-item active">
            <router-link class="nav-link" :to="{ name: 'Todos' }"
              >Todos</router-link
            >
          </li>
        </ul>
        <form class="form-inline my-2 my-lg-0" style="display: flex">
          <input
            class="form-control mr-sm-2"
            style="margin-right: 0.5em"
            type="search"
            placeholder="Search"
            aria-label="Search"
          />
          <button class="btn btn-outline-success my-2 my-sm-0" type="submit">
            Search
          </button>
        </form>
      </div>
    </nav>
    <div class="container">
      <router-view />
    </div>
  </div>
</template>

<script>
export default {};
</script>

<style></style>
