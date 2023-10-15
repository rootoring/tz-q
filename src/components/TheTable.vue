<template>
  <div>
    <button
      @click="saveData()"
      :class="saveBtn ? 'save-btn__active' : ''"
      class="save-btn"
    >
      Сохранить изменения
    </button>
    <div class="settings">
      <button @click="addData" class="settings__add-btn">
        Добавить строку
      </button>
      <!-- <ul>
        <li>
          <input type="checkbox" id="" />
          <label for="">{{}}</label>
        </li>
      </ul> -->
    </div>
    <div class="table-wrap">
      <table ref="draggWrapper" @mousemove="draggMove($event)">
        <tr
          ref="headerDraggWrapper"
          class="header-dragg"
          @mousemove="headDragMove($event)"
        >
          <th></th>
          <th></th>
          <th
            class="daragg-th"
            v-for="(key, index) of headerKeys"
            :key="key"
            ref="headerItems"
            @mousedown.prevent="headDragStart($event, index)"
            @mouseup.prevent="headDrop($event, index)"
          >
            {{ key }}
          </th>
        </tr>
        <tr
          v-for="(row, index) of data"
          :key="row['Кол-во']"
          ref="trDraggItems"
        >
          <td class="number-block">
            <div class="number-block__wrap">
              <div
                @mousedown="onDragStart($event, index)"
                @mouseup="onDrop($event, index)"
                class="grab"
              >
                0
              </div>
              {{ index + 1 }}
            </div>
          </td>
          <td>
            <span><button @click="delData(index)">Удалить</button></span>
          </td>
          <td class="table-td" v-for="key of headerKeys" :key="key">
            <select
              class="td-value"
              v-model="row['Наименование еденицы']"
              v-if="selectFilter(row, key)"
            >
              >
              <option value="Мраморный щебень фр. 2-5 мм, 25кг">
                Мраморный щебень фр. 2-5 мм, 25кг
              </option>
              <option value="Мраморный щебень фр. 2-5 мм, 25кг (белый)">
                Мраморный щебень фр. 2-5 мм, 25кг (белый)
              </option>
            </select>
            <div class="td-value" v-else>{{ row[key] }}</div>
          </td>
        </tr>
      </table>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from "vue";

const isDragIndex = ref(-1);
const headerDraggWrapper = ref(null);
const headerItems = ref(null);
const draggingElem = ref(null);

const draggWrapper = ref(null);
const trDraggIndex = ref(-1);
const trDtaggElem = ref(null);
const trDraggItems = ref(null);
const buttonActive = ref(null);
let x = 0;
let y = 0;

let saveBtn = ref(false);

const selectFilter = (row, key) => {
  let currentKey = Object.keys(row);
  return currentKey[0] == key ? true : false;
};

const addData = () => {
  let col = Math.floor(Math.random() * 99);
  data.value.push({
    "Наименование еденицы": "Мраморный щебень фр. 2-5 мм, 25кг",
    "Кол-во": col,
    Цена: "1",
    "Название товара": "Мраморный щебень",
    Итого: "454",
  });

  saveBtn.value = true;
};

const delData = (index) => {
  data.value.splice(index, 1);
  saveBtn.value = true;
};

const saveData = () => {
  saveBtn.value = false;
};

const headDragStart = (e, index) => {
  if (isDragIndex.value > -1) return;
  draggingElem.value = e.target;
  isDragIndex.value = index;
  draggingElem.value.style.zIndex = `1`;
  draggingElem.value.style.position = `absolute`;
  draggingElem.value.style.border = `none`;
  draggingElem.value.style.left = `${
    e.x - x - draggingElem.value.offsetWidth / 2
  }px`;
};

const headDragMove = (e) => {
  if (draggingElem.value == null) return;

  draggingElem.value.style.left = `${
    e.x - x - draggingElem.value.offsetWidth / 2
  }px`;
  headerItems.value.forEach((element, index) => {
    if (isDragIndex.value === index) return;

    if (
      isDragIndex.value > index &&
      draggingElem.value.getBoundingClientRect().x <
        element.getBoundingClientRect().x
    ) {
      let el1 = headerKeys.value[index];
      let el2 = headerKeys.value[isDragIndex.value];
      headerKeys.value[index] = el2;
      headerKeys.value[isDragIndex.value] = el1;
      let el3 = headerItems.value[index];
      let el4 = headerItems.value[isDragIndex.value];
      headerItems.value[index] = el4;
      headerItems.value[isDragIndex.value] = el3;
      isDragIndex.value = isDragIndex.value - 1;
    }

    if (
      isDragIndex.value < index &&
      draggingElem.value.getBoundingClientRect().x >
        element.getBoundingClientRect().x
    ) {
      let el1 = headerKeys.value[index];
      let el2 = headerKeys.value[isDragIndex.value];
      headerKeys.value[isDragIndex.value] = el1;
      headerKeys.value[index] = el2;
      let el3 = headerItems.value[index];
      let el4 = headerItems.value[isDragIndex.value];
      headerItems.value[index] = el4;
      headerItems.value[isDragIndex.value] = el3;
      isDragIndex.value = isDragIndex.value + 1;
    }
  });
};
const headDrop = () => {
  isDragIndex.value = -1;
  draggingElem.value.style.zIndex = `0`;
  draggingElem.value.style.position = `static`;
  draggingElem.value.style.left = `0px`;
  draggingElem.value.style.borderLeft = `solid 1px #eeeff1`;
  draggingElem.value = null;
};

const onDragStart = (e, index) => {
  if (trDraggIndex.value > -1) return;
  let div = document.createElement("tr");
  div.classList.add("dropArea");
  trDtaggElem.value = trDraggItems.value.find((item, i) => {
    if (i === index) return item;
  });
  trDtaggElem.value.before(div);
  buttonActive.value = e.target;
  trDraggIndex.value = index;
  trDtaggElem.value.style.zIndex = "1";
  trDtaggElem.value.style.position = "absolute";
  trDtaggElem.value.style.top = `${
    e.y - y - trDtaggElem.value.offsetHeight / 2
  }px`;
};

const draggMove = (e) => {
  let dropArea = document.getElementsByClassName("dropArea");
  let div = document.createElement("tr");
  div.classList.add("dropArea");

  if (trDraggIndex.value == -1) return;
  trDtaggElem.value.style.top = `${
    e.y - y - trDtaggElem.value.offsetHeight / 2
  }px`;
  if (e.target != buttonActive.value) {
    trDraggIndex.value = -1;
    trDtaggElem.value.style.zIndex = `0`;
    trDtaggElem.value.style.position = `static`;
    trDtaggElem.value.style.top = `0px`;
    trDtaggElem.value = null;
    if (dropArea[0]) {
      dropArea[0].remove();
    }
  }
  trDraggItems.value.forEach((element, index) => {
    if (
      trDraggIndex.value > index &&
      element.getBoundingClientRect().y >
        trDtaggElem.value.getBoundingClientRect().y
    ) {
      if (dropArea[0]) {
        dropArea[0].remove();
      }
      element.before(div);
      let el1 = data.value[index];
      let el2 = data.value[trDraggIndex.value];
      data.value[index] = el2;
      data.value[trDraggIndex.value] = el1;
      let el3 = trDraggItems.value[index];
      let el4 = trDraggItems.value[trDraggIndex.value];
      trDraggItems.value[index] = el4;
      trDraggItems.value[trDraggIndex.value] = el3;
      trDraggIndex.value = trDraggIndex.value - 1;
    }
    if (
      trDraggIndex.value < index &&
      element.getBoundingClientRect().y <
        trDtaggElem.value.getBoundingClientRect().y
    ) {
      if (dropArea[0]) {
        dropArea[0].remove();
      }
      element.after(div);
      let el1 = data.value[index];
      let el2 = data.value[trDraggIndex.value];
      data.value[index] = el2;
      data.value[trDraggIndex.value] = el1;
      let el3 = trDraggItems.value[index];
      let el4 = trDraggItems.value[trDraggIndex.value];
      trDraggItems.value[index] = el4;
      trDraggItems.value[trDraggIndex.value] = el3;
      trDraggIndex.value = trDraggIndex.value + 1;
    }
  });
};

const onDrop = () => {
  let dropArea = document.getElementsByClassName("dropArea");
  if (dropArea[0]) {
    dropArea[0].remove();
  }
  trDraggIndex.value = -1;
  trDtaggElem.value.style.zIndex = `0`;
  trDtaggElem.value.style.position = `static`;
  trDtaggElem.value.style.top = `0px`;
  trDtaggElem.value = null;
};

onMounted(() => {
  x = headerDraggWrapper.value.getBoundingClientRect().x;
  y = draggWrapper.value.getBoundingClientRect().y;
});

const headerKeys = ref([
  "Наименование еденицы",
  "Кол-во",
  "Цена",
  "Название товара",
  "Итого",
]);

const data = ref([
  {
    "Наименование еденицы": "Мраморный щебень фр. 2-5 мм, 25кг",
    "Кол-во": "100",
    Цена: "1",
    "Название товара": "Мраморный щебень",
    Итого: "454",
  },
  {
    "Наименование еденицы": "Мраморный щебень фр. 2-5 мм, 25кг",
    "Кол-во": "10",
    Цена: "2",
    "Название товара": "Мраморный щебень",
    Итого: "454",
  },
  {
    "Наименование еденицы": "Мраморный щебень фр. 2-5 мм, 25кг",
    "Кол-во": "00",
    Цена: "3",
    "Название товара": "Мраморный щебень",
    Итого: "454",
  },
  {
    "Наименование еденицы": "Мраморный щебень фр. 2-5 мм, 25кг",
    "Кол-во": "1033",
    Цена: "4",
    "Название товара": "Мраморный щебень",
    Итого: "454",
  },
  {
    "Наименование еденицы": "Мраморный щебень фр. 2-5 мм, 25кг",
    "Кол-во": "1023230",
    Цена: "5",
    "Название товара": "Мраморный щебень",
    Итого: "454",
  },
]);
</script>
<style lang="scss">
.save-btn {
  position: fixed;
  bottom: -80px;
  right: 80px;
  transition: 0.3s ease-out;
}
.save-btn__active {
  bottom: 80px;
}
tr.dropArea {
  height: 55px;
  border-radius: 5px;
  border: dashed 2px #a6b7d4;
  background: #fbfcfd;
}
.table-wrap {
  background: #ffffff;
  padding: 9px 5px 25px;
  border-radius: 10px;
  box-shadow: 0 5px 20px 0 rgba(0, 0, 0, 0.07);
  border: solid 1px #eeeff1;
  width: fit-content;
  margin-top: 50px;
}
table {
  border-collapse: collapse;
  user-select: none;
  position: relative;

  .header-dragg {
    position: relative;
    border: solid 1px #eeeff1;
    border-left: none;
    border-right: none;
    width: 100%;

    th + th {
      border-left: solid 1px #eeeff1;
    }
    th {
      position: relative;
      left: 0;
      top: 0;
      text-align: left;
    }
    .daragg-th {
      min-width: 100px;
      cursor: ew-resize;
    }
  }

  tr {
    background: #ffffff;
    width: 100%;

    .number-block {
      .number-block__wrap {
        align-items: center;
        justify-content: center;
        display: flex;
        height: 20px;
        width: 25px;
        .grab {
          width: 100%;
          margin-right: 5px;
          height: 100%;
          background-image: url("drag.svg");
          background-size: contain;
          background-repeat: no-repeat;
          color: rgba($color: #000000, $alpha: 0);
          cursor: s-resize;
          background-position: center;
        }
      }
    }

    td {
      .td-value {
        padding: 10px 5px 1px 15px;
        border-radius: 5px;
        border: solid 1px #ccc;
        height: 35px;
      }
    }
  }
}

th,
td {
  padding: 10px;
}

.settings {
  margin-top: 45px;

  .settings__add-btn {
    margin-left: 25px;
  }
}
</style>
