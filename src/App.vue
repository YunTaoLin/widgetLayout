<template>
  <div id="app">
    <div class="container-fluid">
      <div class="wrapping">
        <div class="widget accordion" role="tablist">
          <h2 v-b-toggle="'IM'" class="tab IM mb-3">IM</h2>
          <!-- Element to collapse -->
          <b-collapse id="IM" accordion="my-accordion">
            <div
              v-for="(subService, index) in subServiceList.slice(0, 3)"
              :key="index"
            >
              <div
                :class="`subService_${index}`"
                :style="`width:${(subService.minW / 4) * 100}%`"
              ></div>
            </div>
          </b-collapse>

          <h2 v-b-toggle="'DR'" class="tab DR mb-3 mt-5">DR</h2>
          <b-collapse id="DR" accordion="my-accordion">
            <div
              v-for="(subService, index) in subServiceList.slice(3, 6)"
              :key="index + 3"
            >
              <div
                :class="`subService_${index + 3}`"
                :style="`width:${(subService.minW / 4) * 100}%`"
              ></div>
            </div>
          </b-collapse>

          <h2 v-b-toggle="'DC'" class="tab DC mb-3 mt-5">DC</h2>
          <b-collapse id="DC" accordion="my-accordion">
            <div
              v-for="(subService, index) in subServiceList.slice(6, 9)"
              :key="index + 6"
            >
              <div
                :class="`subService_${index + 6}`"
                :style="`width:${(subService.minW / 4) * 100}%`"
              ></div>
            </div>
          </b-collapse>
        </div>
        <div class="dashboard">
          <div class="grid-stack"></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { GridStack } from "gridstack";
export default {
  name: "App",
  components: {},
  data() {
    // Reference to the GridStack instance to access it later
    return {
      subServiceGrid: {},
      grid: undefined,
      count: 0,
      info: "",
      timerId: undefined,
      subServiceList: [
        { x: 0, y: 0, w: 4, h: 1, minW: 4, content: "IM - 4*1", type: "IM" },
        { x: 0, y: 1, w: 2, h: 1, minW: 2, content: "IM - 2*1", type: "IM" },
        { x: 0, y: 2, w: 2, h: 2, minW: 2, content: "IM - 2*2", type: "IM" },
        { x: 0, y: 0, w: 4, h: 1, minW: 4, content: "DR - 4*1", type: "DR" },
        { x: 0, y: 1, w: 2, h: 1, minW: 2, content: "DR - 2*1", type: "DR" },
        { x: 0, y: 2, w: 2, h: 2, minW: 2, content: "DR - 2*2", type: "DR" },
        { x: 0, y: 0, w: 4, h: 1, minW: 4, content: "DC - 4*1", type: "DC" },
        { x: 0, y: 1, w: 2, h: 1, minW: 2, content: "DC - 2*1", type: "DC" },
        { x: 0, y: 2, w: 2, h: 2, minW: 2, content: "DC - 2*2", type: "DC" },
      ],
    };
  },
  watch: {
    /**
     * Clear the info text after a two second timeout. Clears previous timeout first.
     */
    info: function (newVal) {
      if (newVal.length === 0) return;

      window.clearTimeout(this.timerId);
      this.timerId = window.setTimeout(() => {
        this.info = "";
      }, 2000);
    },
  },
  mounted: function () {
    // Provides access to the GridStack instance across the Vue component.
    this.grid = GridStack.init({
      column: 4,
      float: true,
      cellHeight: "170px",
      cellWidth: "170px",
      minRow: 4,
      maxRow: 4,
      disableResize: true,
      acceptWidgets: true,
      margin: 0,
      alwaysShowResizeHandle: false,
      disableOneColumnMode: true,
      dragInOptions: { appendTo: "body", helper: "clone" },
      removable: true,
    });

    //子服務
    this.subServiceList.forEach((subService, index) => {
      this.subServiceGrid[`subService_${index}`] = GridStack.init(
        {
          column: this.subServiceList[index].minW,
          float: true,
          cellHeight: "60px",
          // cellWidth: "100px",
          minRow: this.subServiceList[index].h,
          maxRow: this.subServiceList[index].h,
          disableResize: true,
          acceptWidgets: false,
          alwaysShowResizeHandle: false,
          disableOneColumnMode: true,
          margin: 0,
          itemClass: subService.type,
          dragInOptions: { appendTo: "body", helper: "clone" },
        },
        `.subService_${index}`
      );
      this.initWidget(index);
      this.subServiceGrid[`subService_${index}`].on("dragstart", () => {
        this.initWidget(index);
      });
    });

    //掛勾拖動
    // this.grid.setupDragIn({
    //   dragInOptions: {
    //     helper: "clone",
    //   },
    // });
  },
  methods: {
    // addNewWidget() {
    //   const node = this.items[0];
    //   node.id = node.content = String(this.count++);
    //   this.grid.addWidget(node);
    // },
    initWidget(index) {
      let node = this.subServiceList[index];
      node.id = index + 1;
      this.subServiceGrid[`subService_${index}`].addWidget(node);
    },
  },
};
</script>

<style lang="scss">
@import "gridstack/dist/gridstack.min.css";
@import "gridstack/dist/gridstack-extra.min.css";
/* Optional styles for demos */
html,
body {
  margin: 0;
  padding: 0;
  width: 100%;
}
#app {
  padding: 40px;
}

.wrapping {
  width: 100%;
  display: flex;
  .widget {
    width: 240px;
    display: flex;
    flex-direction: column;
    > div h2 {
      margin-top: 40px;
    }
    > div:first-of-type h2 {
      margin-top: 0;
    }
  }
  .dashboard {
    flex-grow: 1;
    padding: 0 120px;
  }
}

.grid-stack {
  background: #ddd;
  border: 1px solid #000;
  margin-bottom: 12px;
}

.tab {
  font-weight: 800;
  &.IM,
  &.IM:hover {
    color: #3e6b7e;
    border-bottom: 3px solid #3e6b7e;
  }
  &.DR,
  &.DR:hover {
    color: #fdb328;
    border-bottom: 3px solid #fdb328;
  }
  &.DC,
  &.DC:hover {
    color: #EB6C93;
    border-bottom: 3px solid #EB6C93;
  }
}
.grid-stack-item {
  text-align: center;
  color: #fff;
  border: 1px solid #000;
  cursor: pointer;
  &.IM {
    background-color: #3e6b7e;
  }
  &.DR {
    background-color: #fdb328;
  }
  &.DC {
    background-color: #EB6C93;
  }
}

.grid-stack-item-content {
  display: flex;
  justify-content: center;
  align-items: center;
}

.grid-stack-item-removing {
  opacity: 0.5;
}
.trash {
  height: 100px;
  background: rgba(255, 0, 0, 0.1) center center
    url(data:image/svg+xml;utf8;base64,PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iaXNvLTg4NTktMSI/Pgo8IS0tIEdlbmVyYXRvcjogQWRvYmUgSWxsdXN0cmF0b3IgMTYuMC4wLCBTVkcgRXhwb3J0IFBsdWctSW4gLiBTVkcgVmVyc2lvbjogNi4wMCBCdWlsZCAwKSAgLS0+CjwhRE9DVFlQRSBzdmcgUFVCTElDICItLy9XM0MvL0RURCBTVkcgMS4xLy9FTiIgImh0dHA6Ly93d3cudzMub3JnL0dyYXBoaWNzL1NWRy8xLjEvRFREL3N2ZzExLmR0ZCI+CjxzdmcgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIiB4bWxuczp4bGluaz0iaHR0cDovL3d3dy53My5vcmcvMTk5OS94bGluayIgdmVyc2lvbj0iMS4xIiBpZD0iQ2FwYV8xIiB4PSIwcHgiIHk9IjBweCIgd2lkdGg9IjY0cHgiIGhlaWdodD0iNjRweCIgdmlld0JveD0iMCAwIDQzOC41MjkgNDM4LjUyOSIgc3R5bGU9ImVuYWJsZS1iYWNrZ3JvdW5kOm5ldyAwIDAgNDM4LjUyOSA0MzguNTI5OyIgeG1sOnNwYWNlPSJwcmVzZXJ2ZSI+CjxnPgoJPGc+CgkJPHBhdGggZD0iTTQxNy42ODksNzUuNjU0Yy0xLjcxMS0xLjcwOS0zLjkwMS0yLjU2OC02LjU2My0yLjU2OGgtODguMjI0TDMwMi45MTcsMjUuNDFjLTIuODU0LTcuMDQ0LTcuOTk0LTEzLjA0LTE1LjQxMy0xNy45ODkgICAgQzI4MC4wNzgsMi40NzMsMjcyLjU1NiwwLDI2NC45NDUsMGgtOTEuMzYzYy03LjYxMSwwLTE1LjEzMSwyLjQ3My0yMi41NTQsNy40MjFjLTcuNDI0LDQuOTQ5LTEyLjU2MywxMC45NDQtMTUuNDE5LDE3Ljk4OSAgICBsLTE5Ljk4NSw0Ny42NzZoLTg4LjIyYy0yLjY2NywwLTQuODUzLDAuODU5LTYuNTY3LDIuNTY4Yy0xLjcwOSwxLjcxMy0yLjU2OCwzLjkwMy0yLjU2OCw2LjU2N3YxOC4yNzQgICAgYzAsMi42NjQsMC44NTUsNC44NTQsMi41NjgsNi41NjRjMS43MTQsMS43MTIsMy45MDQsMi41NjgsNi41NjcsMi41NjhoMjcuNDA2djI3MS44YzAsMTUuODAzLDQuNDczLDI5LjI2NiwxMy40MTgsNDAuMzk4ICAgIGM4Ljk0NywxMS4xMzksMTkuNzAxLDE2LjcwMywzMi4yNjQsMTYuNzAzaDIzNy41NDJjMTIuNTY2LDAsMjMuMzE5LTUuNzU2LDMyLjI2NS0xNy4yNjhjOC45NDUtMTEuNTIsMTMuNDE1LTI1LjE3NCwxMy40MTUtNDAuOTcxICAgIFYxMDkuNjI3aDI3LjQxMWMyLjY2MiwwLDQuODUzLTAuODU2LDYuNTYzLTIuNTY4YzEuNzA4LTEuNzA5LDIuNTctMy45LDIuNTctNi41NjRWODIuMjIxICAgIEM0MjAuMjYsNzkuNTU3LDQxOS4zOTcsNzcuMzY3LDQxNy42ODksNzUuNjU0eiBNMTY5LjMwMSwzOS42NzhjMS4zMzEtMS43MTIsMi45NS0yLjc2Miw0Ljg1My0zLjE0aDkwLjUwNCAgICBjMS45MDMsMC4zODEsMy41MjUsMS40Myw0Ljg1NCwzLjE0bDEzLjcwOSwzMy40MDRIMTU1LjMxMUwxNjkuMzAxLDM5LjY3OHogTTM0Ny4xNzMsMzgwLjI5MWMwLDQuMTg2LTAuNjY0LDguMDQyLTEuOTk5LDExLjU2MSAgICBjLTEuMzM0LDMuNTE4LTIuNzE3LDYuMDg4LTQuMTQxLDcuNzA2Yy0xLjQzMSwxLjYyMi0yLjQyMywyLjQyNy0yLjk5OCwyLjQyN0gxMDAuNDkzYy0wLjU3MSwwLTEuNTY1LTAuODA1LTIuOTk2LTIuNDI3ICAgIGMtMS40MjktMS42MTgtMi44MS00LjE4OC00LjE0My03LjcwNmMtMS4zMzEtMy41MTktMS45OTctNy4zNzktMS45OTctMTEuNTYxVjEwOS42MjdoMjU1LjgxNVYzODAuMjkxeiIgZmlsbD0iI2ZmOWNhZSIvPgoJCTxwYXRoIGQ9Ik0xMzcuMDQsMzQ3LjE3MmgxOC4yNzFjMi42NjcsMCw0Ljg1OC0wLjg1NSw2LjU2Ny0yLjU2N2MxLjcwOS0xLjcxOCwyLjU2OC0zLjkwMSwyLjU2OC02LjU3VjE3My41ODEgICAgYzAtMi42NjMtMC44NTktNC44NTMtMi41NjgtNi41NjdjLTEuNzE0LTEuNzA5LTMuODk5LTIuNTY1LTYuNTY3LTIuNTY1SDEzNy4wNGMtMi42NjcsMC00Ljg1NCwwLjg1NS02LjU2NywyLjU2NSAgICBjLTEuNzExLDEuNzE0LTIuNTY4LDMuOTA0LTIuNTY4LDYuNTY3djE2NC40NTRjMCwyLjY2OSwwLjg1NCw0Ljg1MywyLjU2OCw2LjU3QzEzMi4xODYsMzQ2LjMxNiwxMzQuMzczLDM0Ny4xNzIsMTM3LjA0LDM0Ny4xNzJ6IiBmaWxsPSIjZmY5Y2FlIi8+CgkJPHBhdGggZD0iTTIxMC4xMjksMzQ3LjE3MmgxOC4yNzFjMi42NjYsMCw0Ljg1Ni0wLjg1NSw2LjU2NC0yLjU2N2MxLjcxOC0xLjcxOCwyLjU2OS0zLjkwMSwyLjU2OS02LjU3VjE3My41ODEgICAgYzAtMi42NjMtMC44NTItNC44NTMtMi41NjktNi41NjdjLTEuNzA4LTEuNzA5LTMuODk4LTIuNTY1LTYuNTY0LTIuNTY1aC0xOC4yNzFjLTIuNjY0LDAtNC44NTQsMC44NTUtNi41NjcsMi41NjUgICAgYy0xLjcxNCwxLjcxNC0yLjU2OCwzLjkwNC0yLjU2OCw2LjU2N3YxNjQuNDU0YzAsMi42NjksMC44NTQsNC44NTMsMi41NjgsNi41N0MyMDUuMjc0LDM0Ni4zMTYsMjA3LjQ2NSwzNDcuMTcyLDIxMC4xMjksMzQ3LjE3MnogICAgIiBmaWxsPSIjZmY5Y2FlIi8+CgkJPHBhdGggZD0iTTI4My4yMiwzNDcuMTcyaDE4LjI2OGMyLjY2OSwwLDQuODU5LTAuODU1LDYuNTctMi41NjdjMS43MTEtMS43MTgsMi41NjItMy45MDEsMi41NjItNi41N1YxNzMuNTgxICAgIGMwLTIuNjYzLTAuODUyLTQuODUzLTIuNTYyLTYuNTY3Yy0xLjcxMS0xLjcwOS0zLjkwMS0yLjU2NS02LjU3LTIuNTY1SDI4My4yMmMtMi42NywwLTQuODUzLDAuODU1LTYuNTcxLDIuNTY1ICAgIGMtMS43MTEsMS43MTQtMi41NjYsMy45MDQtMi41NjYsNi41Njd2MTY0LjQ1NGMwLDIuNjY5LDAuODU1LDQuODUzLDIuNTY2LDYuNTdDMjc4LjM2NywzNDYuMzE2LDI4MC41NSwzNDcuMTcyLDI4My4yMiwzNDcuMTcyeiIgZmlsbD0iI2ZmOWNhZSIvPgoJPC9nPgo8L2c+CjxnPgo8L2c+CjxnPgo8L2c+CjxnPgo8L2c+CjxnPgo8L2c+CjxnPgo8L2c+CjxnPgo8L2c+CjxnPgo8L2c+CjxnPgo8L2c+CjxnPgo8L2c+CjxnPgo8L2c+CjxnPgo8L2c+CjxnPgo8L2c+CjxnPgo8L2c+CjxnPgo8L2c+CjxnPgo8L2c+Cjwvc3ZnPgo=)
    no-repeat;
}
.sidebar {
  background: rgba(0, 255, 0, 0.1);
  padding: 25px 0;
  height: 100px;
  text-align: center;
}
.sidebar .grid-stack-item {
  width: 120px;
  height: 50px;
  border: 2px dashed green;
  text-align: center;
  line-height: 35px;
  z-index: 10;
  background: rgba(0, 255, 0, 0.1);
  cursor: default;
  display: inline-block;
}
.sidebar .grid-stack-item .grid-stack-item-content {
  background-color: #faa;
}

/* make nested grid have slightly darker bg take almost all space (need some to tell them apart) so items inside can have similar to external size+margin */
.grid-stack > .grid-stack-item.grid-stack-sub-grid > .grid-stack-item-content {
  background: rgba(0, 0, 0, 0.1);
  inset: 0 2px;
}
.grid-stack.grid-stack-nested {
  background: none;
  /* background-color: red; */
  /* take entire space */
  position: absolute;
  inset: 0; /* TODO change top: if you have content in nested grid */
}

//subService
</style>
