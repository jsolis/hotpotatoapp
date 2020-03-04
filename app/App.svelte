<page>
    <actionBar title="Hot Potato" />

    <gridLayout columns="*,120" rows="70,*">
        <!-- Configures the text field and ensures that pressing Return on the keyboard
            produces the same result as tapping the button. -->
        <textField col="0" row="0" bind:text="{searchTerm}" hint="Search movies..." editable="true"
            on:returnPress="{searchMovies}" />
        <button col="1" row="0" text="Search" on:tap="{searchMovies}" class="-primary" />

        <label text={loading} row="1" colspan="2"/>

        <listView items="{movies}" on:itemTap="{onItemTap}" row="2" colSpan="2">
            <Template let:item>
                <image src="{item.images.poster[0]}" stretch="none" />
                <label text="{`${item.original_title} (${item.released})`}" textWrap="true" />
                <label text="{getInLibrary(item)}" />
                {#if showDownloadButton(item)}
                    <button text="Download" on:tap="{() => downloadMovie(item)}" />
                {/if}
            </Template>
        </listView>
    </gridLayout>
</page>

<script>
    import { Template } from 'svelte-native/components'
    import { alert } from 'tns-core-modules/ui/dialogs'
    import { navigate } from 'svelte-native'

    import MovieDetails from './MovieDetails.svelte'

    let movies = []
    let searchTerm = ""
    let loading = ""

    async function searchMovies() {
        if (searchTerm === "") searchTerm = "frozen";
        movies = []
        loading = "loading..."
        let response
        try {
            response = await fetch("https://hubot.coffee/proxy", 
                {
                    method: "GET",
                    headers: {
                        fetchURL: `http://hp.mags24.com/hotpotato/search/${searchTerm}`,
                        Authorization: "foobar"
                    }
                })
        } catch(e) {
            console.log('catch', e)
        }
        const moviesResponse = await response.json()
        loading = ""
        movies = moviesResponse.movies
    }

    function onItemTap(args) {
        navigate({
            page: MovieDetails,
            props: { imdb: movies[args.index].imdb }
        })
    }

    function getInLibrary(movie) {
        if (movie.in_library) return "In Library"
        else if (movie.in_wanted) return "In Download Queue"
        else return ""
    }

    function showDownloadButton(movie) {
        return (!movie.in_library && !movie.in_wanted)
    }

    async function downloadMovie(movie) {
        const imdb = movie.imdb
        const title = escape(movie.original_title)
        const url = `http://hp.mags24.com/hotpotato/movie.add/${imdb}/${title}`;
        const response = await fetch(url)
        const downloadResponse = await response.json()
        if (downloadResponse.success) alert(`Downloading ${movie.original_title}`)
        else alert(`Failed to Download ${movie.original_title}`)
    }
    
</script>

<style>
    textField {
        font-size: 20;
    }
</style>
