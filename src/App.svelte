<script>
    import Header from "./components/Header.svelte";
    import Input from "./components/Input.svelte";
    import { words } from "popular-english-words";

    let sortType = 2;
    let popularity = "";
    let regexInput = "";
    let includeInput = "";
    let excludeInput = "";
    let letterCount = "";
    let output = "";

    const findWords = () => {
        console.log(sortType);
        console.log(popularity);
        console.log(regexInput);
        console.log(includeInput);
        console.log(excludeInput);

        const alphabet = [
            "a",
            "b",
            "c",
            "d",
            "e",
            "f",
            "g",
            "h",
            "i",
            "j",
            "k",
            "l",
            "m",
            "n",
            "o",
            "p",
            "q",
            "r",
            "s",
            "t",
            "u",
            "v",
            "w",
            "x",
            "y",
            "z",
        ];
        const alphabet2DArray = [];

        const regexFind = RegExp(regexInput, "i");

        var formattedPopularity = popularity.match(/\d+/);

        var formattedIncludeString = String(includeInput)
            .split("")
            .filter((letter) => letter.match(/[a-zA-Z]/))
            .map((letter)=>`(?=.*${letter})`)
            .join("");
        const regexInclude = RegExp(`${String(formattedIncludeString)}`, "i");

        var formattedExcludeString = String(excludeInput)
            .split("")
            .filter((letter) => letter.match(/[a-zA-Z]/))
            .join("|");
        const regexExclude = RegExp(
            `[a-z]{0,5}[${String(formattedExcludeString)}][a-z]{0,5}`,
            "i"
        );

        let wordList = [];

        // Fetch the words
        if (formattedPopularity) {
            wordList = words.getMostPopularLength(parseInt(formattedPopularity[0]), 5);
        } else {
            wordList = words.getAll();
            wordList = wordList.filter((word) => word.length === 5);
        }

        // Sort the words
        if (sortType === 1) {
            wordList = wordList.sort((a, b) => {
                return a.localeCompare(b);
            });
        }

        // Filter the words
        if (String(regexInput).length > 0) {
            wordList = wordList.filter((word) => regexFind.test(word));
        }

        if (String(includeInput).length > 0) {
            wordList = wordList.filter((word) => regexInclude.test(word));
        }

        if (String(excludeInput).length > 0) {
            wordList = wordList.filter((word) => !regexExclude.test(word));
        }

        //Count the alphabets
        const stringWordList = wordList.join(" ");
        let countString = "";

        alphabet.forEach((letter) => {
            alphabet2DArray.push([
                letter,
                (stringWordList.match(RegExp(`${String(letter)}`, "g")) || [])
                    .length,
            ]);
        });
        alphabet2DArray.sort((a, b) => b[1] - a[1]);

        countString = alphabet2DArray
            .map((letterArray) => {
                return `${letterArray[0]}: ${letterArray[1]}`;
            })
            .join("   ");

        letterCount = countString;
        output = stringWordList;
    };
</script>

<article class="app-body">
    <Header />

    <section class="input-container">
        <section class="sort-by-section">
            Sort Order:
            <input
                type="radio"
                id="alpha-sort"
                name="sort-by"
                value={1}
                bind:group={sortType}
            />
            <label for="alpha-sort">Alphabetical</label>
            <input
                type="radio"
                id="popu-sort"
                name="sort-by"
                value={2}
                bind:group={sortType}
            />
            <label for="popu-sort">Popularity</label>
        </section>

        <Input
            labelText="Popularity:"
            id="popularity-input"
            tooltipText="Numbers Only! The higher the number, the more words will show up in the result."
            bind:inputValue={popularity}
            onKeypress={(e) => {
                if (e.key === "Enter") {
                    findWords();
                }
            }}
        />

        <Input
            labelText="Regex Filter:"
            id="regex-input"
            tooltipText="Pro Tip: .[^z]... means Z cannot be the second letter of the word.  Useful for yellows."
            bind:inputValue={regexInput}
            onKeypress={(e) => {
                if (e.key === "Enter") {
                    findWords();
                }
            }}
        />

        <div class="input-row">
            <label class="input-label" for="regex-input">Include Letters:</label
            >
            <input
                id="include-input"
                class="input-boxes"
                bind:value={includeInput}
                on:keypress={(e) => {
                    if (e.key === "Enter") {
                        findWords();
                    }
                }}
            />
        </div>

        <div class="input-row">
            <label class="input-label" for="regex-input"
                >Excluded Letters:</label
            >
            <input
                id="exclude-input"
                class="input-boxes"
                bind:value={excludeInput}
                on:keypress={(e) => {
                    if (e.key === "Enter") {
                        findWords();
                    }
                }}
            />
        </div>
    </section>

    <button class="input-button" on:click={findWords}>FIND A WORD</button>

    <section class="output-container">
        <div class="output-text">
            {letterCount ? letterCount : "Letter Count"}
        </div>
        <div class="output-text">{output ? output : "Potential Words"}</div>
    </section>
</article>

<style>
    .app-body {
        display: flex;
        flex-direction: column;
        margin-bottom: 10px;
        align-items: center;
        text-align: center;
        font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
    }

    /* INPUT STYLES */
    .sort-by-section {
        display: flex;
        flex-direction: row;
        align-items: center;
        justify-content: center;
        margin-bottom: 20px;
    }

    .sort-by-section {
        display: flex;
        flex-direction: row;
        align-items: center;
        justify-content: center;
        margin-bottom: 20px;
    }

    .sort-by-section label {
        margin-left: 5px;
    }

    .sort-by-section input {
        margin-left: 15px;
    }

    .input-container {
        display: flex;
        flex-direction: column;
        align-items: center;
        width: 100%;
    }

    .input-row {
        display: flex;
        flex-direction: row;
        margin-bottom: 5px;
    }

    .input-label {
        width: 150px;
    }

    .input-boxes {
        font-size: 12px;
    }

    /* BUTTON STYLES */
    .input-button {
        width: fit-content;
        padding: 5px 10px;
        color: white;
        font-weight: bold;
        background: #ff7400;
        border: 2px solid darkgrey;
        border-radius: 6px;
        margin: 20px 0px;
    }

    /* OUTPUT STYLES */
    .output-container {
        width: 90%;
        display: flex;
        flex-direction: column;
        margin: 10px 0px;
        justify-content: center;
        align-items: center;
    }

    .output-text {
        font-size: large;
        font-family: "Courier New", Courier, monospace;
        margin-bottom: 15px;
    }
</style>
