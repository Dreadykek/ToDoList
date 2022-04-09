<template>
  <Page class="page">
    <ActionBar title="My Tasks" class="action-bar" />

    <TabView height="100%" androidTabsPosition="bottom"
             selectedTabTextColor="#53ba82" tabTextFontSize="15">
      <TabViewItem title="To Do" textTransform="uppercase">
        <!-- Positions an input field, a button, and the list of tasks in a vertical stack. -->
        <StackLayout orientation="vertical" width="100%"
                     height="100%">

          <GridLayout
              columns="2*,*"
              rows="*"
              width="100%"
              height="70"
          >
            <TextField col="0" row="0" v-model="textFieldValue"
                       hint="Type new task..." editable="true"
                       @returnPress="onButtonTap"/>

            <Button col="1" row="0" text="Add task"
                    @tap="onButtonTap" />
          </GridLayout>

          <ListView class="list-group" for="todo in todos"
                    @itemTap="onItemTap" style="height:75%"
                    separatorColor="blue">
            <v-template>
              <Label id="active-task" :text="todo.name"
                     class="list-group-item-heading"
                     textWrap="true" />
            </v-template>
          </ListView>
        </StackLayout>
      </TabViewItem>
      <TabViewItem title="Completed" textTransform="uppercase">
        <ListView class="list-group" for="done in dones"
                  @itemTap="onDoneTap" style="height:10%">
          <v-template>
            <Label :text="done.name"
                   class="list-group-item-heading" textWrap="true"
                   id="completed-task" />
          </v-template>
        </ListView>
      </TabViewItem>
    </TabView>
  </Page>
</template>

<script>
import * as appSettings from "@nativescript/core/application-settings";
export default {
  methods: {
    onItemTap: function(args) {
      action("What do you want to do with this task?", "Cancel",
          [
            "Mark completed",
            "Delete forever"
          ]).then(result => {
        switch (result) {
          case "Mark completed":
            this.dones.unshift(args.item);
            this.todos.splice(args.index, 1);
            this.setDones()
            this.setTodos()
            break;
          case "Delete forever":
            this.todos.splice(args.index, 1);
            this.setTodos()
            break;
          case "Cancel" ||
          undefined: // Dismisses the dialog
            break;
        }
      });
    },
    onDoneTap: function(args) {
      action("What do you want to do with this task?", "Cancel",
          [
            "Mark to do",
            "Delete forever"
          ]).then(result => {
        switch (result) {
          case "Mark to do":
            this.todos.unshift(args.item);
            this.dones.splice(args.index, 1);
            this.setDones()
            this.setTodos()
            break;
          case "Delete forever":
            this.dones.splice(args.index, 1);
            this.setDones()
            break;
          case "Cancel" || undefined:
            break;
        }
      });
    },
    onButtonTap() {
      if (this.textFieldValue === "")
        return;

      this.todos.unshift({
        name: this.textFieldValue

      });
      this.textFieldValue = "";
      this.setTodos()
    },
    getTodos() {
      return JSON.parse(appSettings.getString("todos", "[]"))

    },
    getDones() {
      return JSON.parse(appSettings.getString("dones", "[]"))
    },
    setTodos() {
      appSettings.setString("todos", JSON.stringify(this.todos));
    },
    setDones() {
      appSettings.setString("dones", JSON.stringify(this.dones));
    }
  },
  data() {
    return {
      dones: this.getDones(),
      todos: this.getTodos(),
      textFieldValue: ""
    };
  }
};
</script>

<style scoped>
.home-panel {
  vertical-align: center;
  font-size: 20;
  margin: 15;
}
.description-label {
  margin-bottom: 15;
}
TextField {
  font-size: 20;
  color: #000000;
  margin-top: 10;
  margin-bottom: 10;
  margin-right: 5;
  margin-left: 20;
}
Button {
  font-size: 20;
  font-weight: bold;
  color: white;
  background-color: blue;
  height: 40;
  margin-top: 10;
  margin-bottom:
      10;
  margin-right: 10;
  margin-left: 10;
  border-radius: 20px;
}
#active-task {
  font-size: 20;
  font-weight: bold;
  color: #000000;
  margin-left: 20;
  padding-top: 5;
  padding-bottom: 10;
}
#completed-task {
  font-size: 20;
  color: #d3d3d3;
  margin-left: 20;
  padding-top: 5;
  padding-bottom: 10;
  text-decoration: line-through;
}
</style>
