<template>
  <Page class="page">
    <ActionBar title="To Do" class="action-bar" />
    <TabView
      height="100%"
      android-tabs-position="bottom"
      selected-tab-text-color="#53ba82"
      tab-text-font-size="15"
    >
      <TabViewItem title="Активные" text-transform="uppercase">
        <StackLayout
          orientation="vertical"
          width="100%"
          height="100%"
        >
          <GridLayout
            columns="2*,*"
            rows="*"
            width="100%"
            height="25%"
          >
            <TextField
              v-model="newTask"
              col="0"
              row="0"
              hint="Новая задача"
              editable="true"
              @returnPress="onButtonTap"
            />
            <Button
              col="1"
              row="0"
              text="Добавить"
              @tap="onButtonTap"
            />
          </GridLayout>
          <ListView
            class="list-group"
            for="todo in todos"
            style="height:75%"
            separator-color="transparent"
            @itemTap="onItemTap"
          >
            <v-template>
              <Label
                id="active-task"
                :text="todo.name"
                class="list-group-item-heading"
                text-wrap="true"
              />
            </v-template>
          </ListView>
        </StackLayout>
      </TabViewItem>

      <TabViewItem title="Выполненные" text-transform="uppercase">
        <ListView
          class="list-group"
          for="done in dones"
          style="height:75%"
          separator-color="transparent"
          @itemTap="onDoneTap"
        >
          <v-template>
            <Label
              id="completed-task"
              :text="done.name"
              class="list-group-item-heading"
              text-wrap="true"
            />
          </v-template>
        </ListView>
      </TabViewItem>
    </TabView>
  </Page>
</template>

<script>

import { getTasks, setTask } from '@/storage.js'

export default {
  data() {
    return {
      dones: [],
      todos: [],
      newTask: '',
    };
  },
  created() {
    this.todos = getTasks('todos')
    this.dones = getTasks('dones')
  },
  methods: {
    onItemTap(args) {

      action('Выберете вариант', 'Закрыть', [
        'Выполнено',
        'Удалить',
      ]).then(result => {
        switch (result) {
          case 'Выполнено':
            this.dones.unshift(args.item);
            this.todos.splice(args.index, 1);
            break;
          case 'Удалить':
            this.todos.splice(args.index, 1);
            break;
          case 'Закрыть' || undefined:
            break;
        }
        setTask('todos', this.todos)
        setTask('dones', this.dones)
      });
    },
    onDoneTap: function(args) {
      action('Выберете вариант', 'Закрыть', [
        'Возобновить',
        'Удалить',
      ]).then(result => {
        switch (result) {
          case 'Возобновить':
            this.todos.unshift(args.item);
            this.dones.splice(args.index, 1);
            break;
          case 'Удалить':
            this.dones.splice(args.index, 1);
            break;
          case 'Закрыть' || undefined:
            break;
        }
        setTask('todos', this.todos)
        setTask('dones', this.dones)
      });
    },
    onButtonTap() {
      if (!this.newTask) {
        return;
      }
      this.todos.unshift({
        name: this.newTask,
      });
      setTask('todos', this.todos)
      this.newTask = '';
    },
  },
};
</script>

<style scoped>
TextField {
  font-size: 20;
  color: #53ba82;
  margin-top: 10;
  margin-bottom: 10;
  margin-right: 5;
  margin-left: 20;
  height: 60;
}
button {
  font-size: 12;
  font-weight: bold;
  color: white;
  background-color: #53ba82;
  height: 40;
  margin-top: 10;
  margin-bottom: 10;
  margin-right: 10;
  margin-left: 10;
  border-radius: 20px;
}
#active-task {
  font-size: 20;
  font-weight: bold;
  color: #53ba82;
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