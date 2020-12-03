<div class="search">
  <input type="text" class="search__input" placeholder="Search" on:input={handleInput} value="{currentOption}">
    {#await results}
      <ul class="search__results">
        <li>Loading...</li>
      </ul>
      {:then list}
        {#if Array.isArray(list) && list.length > 0}
          <ul class="search__results">
          {#each list as item}
            <li on:click={chooseOption(item)}>{item}</li>
          {/each}
          </ul>
        {/if}
    {:catch error}
      <li>Ooops! {error}</li>
    {/await}
</div>

<script>
	import axios from 'axios'

	let isInputInvalid = false
  let currentOption = '';
	let results = [];

	function handleInput({ target }) {
		const { value } = target;

		if (!value) {
			results = undefined;
			return;
		}

		currentOption = target.value;
		results = getSearchResults(currentOption);
	}

	async function getSearchResults(value) {
		const response = await axios.get('http://localhost:5000/data.json');
		return response.data.words.filter(w => w.match(value)).slice(0, 10);
	}

	function chooseOption(option) {
    currentOption = option;
    results = [];
  }
</script>

<style lang="scss">
  .search {
    max-width: 450px;
    margin: auto;
    position: relative;
  }

  .search__results {
    position: absolute;
    width: 100%;
    top: 38px;
    max-height: 340px;
    background-color: #FFF;
    list-style: none;
    padding: 4px 0 0;
    margin: 0;
    border-bottom-left-radius: 4px;
    border-bottom-right-radius: 4px;

    li {
      padding: 5px 7px 7px 16px;
      cursor: pointer;
      user-select: none;
      transition: background-color .2s ease-out;

      &:not(:last-child) {
        border-bottom: 1px solid #e8eaed;
      }

      &:hover {
        background-color: rgba(0, 0, 0, 0.1);
      }
    }
  }

  .search__input {
    outline: 0;
    height: 42px;
    width: 450px;
    line-height: 42px;
    padding: 0 16px;
    background-color: rgba(255, 255, 255, 0.8);
    color: #212121;
    border: 0;
    float: left;
    border-radius: 4px;

    &:focus {
      outline: 0;
      background-color: #FFF;
    }
  }
</style>
