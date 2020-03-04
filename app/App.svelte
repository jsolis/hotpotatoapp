<page>
    <actionBar title="Hot Potato" />

    <gridLayout columns="*,120" rows="70,*">
        <!-- Configures the text field and ensures that pressing Return on the keyboard
            produces the same result as tapping the button. -->
        <textField col="0" row="0" bind:text="{searchTerm}" hint="Search movies..." editable="true"
            on:returnPress="{onButtonTap}" />
        <button col="1" row="0" text="Search" on:tap="{searchMovies}" class="-primary" />

        <label text={loading} row="1" colspan="2"/>

        <listView items="{movies}" on:itemTap="{onItemTap}" row="2" colSpan="2">
            <Template let:item>
                <label text="{item.original_title + ' ' + item.released}" textWrap="true" />
            </Template>
        </listView>
    </gridLayout>
</page>

<script>
    import { Template } from 'svelte-native/components'
    import { alert } from 'tns-core-modules/ui/dialogs'

    let todos = []
    let dones = [] //completed items go here
    let textFieldValue = ""

    let movies = []
    let searchTerm = ""
    let loading = ""

    const removeFromList = (list, item) => list.filter(t => t !== item);
    const addToList = (list, item) => [item, ...list]

    async function searchMovies() {
        //if (searchTerm === "") return;
        movies = []
        loading = "loading..."
        setTimeout(() => {
            loading = ""
            movies = [
                {original_title: 'Frozen', released: "2013-11-27"},
                {original_title: 'Frozen 2', released: "2019-11-20"},
            ]
        }, 1000);

    }

    async function onItemTap(args) {

        alert('details TBD')

        /*
        let result = await action("What do you want to do with this task?", "Cancel", [
            "Mark completed",
            "Delete forever"
        ]);

        console.log(result); // Logs the selected option for debugging.
        let item = todos[args.index]
        switch (result) {
            case "Mark completed":
                dones = addToList(dones, item) // Places the tapped active task at the top of the completed tasks.
                todos = removeFromList(todos, item) // Removes the tapped active task.
                break;
            case "Delete forever":
                todos = removeFromList(todos, item) // Removes the tapped active task.
                break;
            case "Cancel" || undefined: // Dismisses the dialog
                break;
        }
        */
    }

    function onButtonTap() {
        if (textFieldValue === "") return; // Prevents users from entering an empty string.
        console.log("New task added: " + textFieldValue + "."); // Logs the newly added task in the console for debugging.
        todos = [{ name: textFieldValue }, ...todos] // Adds tasks in the ToDo array. Newly added tasks are immediately shown on the screen.
        textFieldValue = ""; // Clears the text field so that users can start adding new tasks immediately.
    }
    
    async function onDoneTap(args) {
      let result = await action("What do you want to do with this task?", "Cancel", [
          "Mark To Do",
          "Delete forever"
      ]);

      console.log(result); // Logs the selected option for debugging.
      let item = dones[args.index]
      switch (result) {
          case "Mark To Do":
              todos = addToList(todos, item) // Places the tapped active task at the top of the completed tasks.
              dones = removeFromList(dones, item) // Removes the tapped active task.
              break;
          case "Delete forever":
              dones = removeFromList(dones, item) // Removes the tapped active task.
              break;
          case "Cancel" || undefined: // Dismisses the dialog
              break;
      }
    }
</script>

<style>
	textField {
			font-size: 20;
  }
  .todo-item-completed {
    color: #939393;
    text-decoration: line-through;
  }
</style>
