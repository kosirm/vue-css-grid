<template>
  <div>
    <div>
      <br>&nbsp;&nbsp;&nbsp; 1.)
      <b>Select element with mouse click</b> (SELECTED ELEMENT HAS RED BORDER).
      <BR/>&nbsp;&nbsp;&nbsp; 2.) Keyboard shortcuts (for selected element):
      <br>&nbsp;&nbsp;&nbsp;
      <b>CTRL + (UP, DOWN, LEFT, RIGHT)</b> - MOVE SELECTED ELEMENT
      <br>&nbsp;&nbsp;&nbsp;
      <b>SHIFT + (UP, DOWN)</b> - MOVE ROW START,
      <b>SHIFT + (LEFT, RIGHT)</b> - MOVE COLUMN START
      <br>&nbsp;&nbsp;&nbsp;
      <b>ALT + (UP, DOWN)</b> - MOVE ROW END,
      <b>ALT + (LEFT, RIGHT)</b> - MOVE COLUMN END
      <br>&nbsp;&nbsp;&nbsp;
      <b>SHIFT + i</b> - MOVE Z-INDEX UP,
      <b>CTRL + i</b> - MOVE Z-INDEX DOWN
      <br>&nbsp;&nbsp;&nbsp;
      <b>SHIFT + (PAGE UP, PAGE DOWN)</b> - CYCLE SELECTION
      <br>&nbsp;&nbsp;&nbsp;
      <b>CTRL + c</b> - CLONE SELECTED ELEMENT
      <br>&nbsp;&nbsp;&nbsp;
      <b>CTRL + DELETE</b> - DELETE SELECTED ELEMENT
      <br>
      <br>
      &nbsp;&nbsp;&nbsp;Window width: {{ ww }}
      <br>
      &nbsp;&nbsp;&nbsp;Window height: {{ wh }}
    </div>
    <div class="bgImg">
      <section id="list" ref="list" :style="list_style">
        <div id="div1" class="div1 box" ref="div1" :style="div1_style"></div>
        <div id="div2" class="div2 box" ref="div2" :style="div2_style"></div>
        <div id="div3" class="div3 box" ref="div3" :style="div3_style"></div>
        <div id="div4" class="div4 box" ref="div4" :style="div4_style"></div>
        <div id="div5" class="div5 box" ref="div5" :style="div5_style"></div>
      </section>
    </div>
    <div class="bgImg" style="width:100%; height:500px;"></div>
  </div>
</template>

<script>
export default {
  data: function() {
    return {
      ww: "",
      wh: "",
      list_style: {
        height: "61.8vw",
        display: "grid",
        gridTemplateColumns:
          "56fr 34fr 56fr 90fr 146fr 236fr 146fr 90fr 56fr 34fr 56fr",
        gridTemplateRows: "56fr 34fr 56fr 90fr 146fr 90fr 56fr 34fr 56fr",
        opacity: 0.9
      },
      div1_style: {
        backgroundColor: "dodgerblue",
        gridArea: "1/1/10/7"
      },
      div2_style: {
        backgroundColor: "#fff333",
        gridArea: "1/6/6/12"
      },
      div3_style: {
        backgroundColor: "goldenrod",
        gridArea: "6/7/7/8"
      },
      div4_style: {
        backgroundColor: "indianred",
        gridArea: "5/8/10/12"
      },
      div5_style: {
        backgroundColor: "mediumseagreen",
        gridArea: "7/7/10/9"
      }
    };
  },
  computed: {},
  methods: {
    getWindowWidth(event) {
      console.log(document.documentElement.clientWidth);
      this.ww = document.documentElement.clientWidth;
    },
    getWindowHeight(event) {
      this.wh = document.documentElement.clientHeight;
    }
  },
  mounted() {
    window.addEventListener("resize", this.getWindowWidth);
    window.addEventListener("resize", this.getWindowHeight);

    let selected = null;
    let wrapper = this.$refs.list;

    wrapper.addEventListener("click", function(e) {
      if (selected !== null) {
        selected.style.border = "";
        selected = null;
        console.log(selected);
      }
      if (
        document.elementFromPoint(e.clientX, e.clientY).closest(".box") !== null
      ) {
        selected = document
          .elementFromPoint(e.clientX, e.clientY)
          .closest(".box");
        selected.style.border = "2px solid red";
      }
    });
    function cssGrid(func) {
      if (!selected) {
        return false;
      } else {
        func(selected);
      }
    }
    function selectNext(el) {
      // console.log(el.nextSibling)
      if (el.nextSibling !== null) {
        selected.style.border = "";
        selected = el.nextSibling;
        selected.style.border = "2px solid red";
      } else {
        selected.style.border = "";
        selected = document.getElementById("list").firstChild;
        selected.style.border = "2px solid red";
      }
    }
    function selectPrevious(el) {
      // console.log(el.previousSibling)
      if (el.previousSibling !== null) {
        selected.style.border = "";
        selected = el.previousSibling;
        selected.style.border = "2px solid red";
      } else {
        selected.style.border = "";
        selected = document.getElementById("list").lastChild;
        selected.style.border = "2px solid red";
      }
    }
    function clone(el) {
      var clone = el.cloneNode(true);
      document.getElementById("list").appendChild(clone);
    }
    function remove(el) {
      var list = document.getElementById("list");
      list.removeChild(el);
    }
    function zIndexUp(el) {
      if (window.getComputedStyle(el).getPropertyValue("z-index") === "auto") {
        el.style.zIndex = 1;
      }
      var zIndex = parseInt(
        window
          .getComputedStyle(el)
          .getPropertyValue("z-index")
          .match(/\d/g)
          .join(""),
        10
      );
      el.style.zIndex = zIndex + 1;
      console.log(window.getComputedStyle(el).getPropertyValue("z-index"));
    }
    function zIndexDown(el) {
      if (
        !selected ||
        window.getComputedStyle(el).getPropertyValue("z-index") === "auto"
      ) {
        return false;
      }

      var zIndex = parseInt(
        window
          .getComputedStyle(el)
          .getPropertyValue("z-index")
          .match(/\d/g)
          .join(""),
        10
      );
      el.style.zIndex = zIndex - 1;
      console.log(window.getComputedStyle(el).getPropertyValue("z-index"));
    }
    function moveLeft(el) {
      var gridColumnStart = parseInt(
        window
          .getComputedStyle(el)
          .getPropertyValue("grid-column-start")
          .match(/\d/g)
          .join(""),
        10
      );
      var gridColumnEnd = parseInt(
        window
          .getComputedStyle(el)
          .getPropertyValue("grid-column-end")
          .match(/\d/g)
          .join(""),
        10
      );
      if (gridColumnStart > 1) {
        el.style.gridColumnStart = gridColumnStart - 1;
        el.style.gridColumnEnd = gridColumnEnd - 1;
      }
    }
    function moveRight(el) {
      var gridColumnMax =
        window
          .getComputedStyle(el.parentNode)
          .getPropertyValue("grid-template-columns")
          .split(" ").length + 1;
      var gridColumnStart = parseInt(
        window
          .getComputedStyle(el)
          .getPropertyValue("grid-column-start")
          .match(/\d/g)
          .join(""),
        10
      );
      var gridColumnEnd = parseInt(
        window
          .getComputedStyle(el)
          .getPropertyValue("grid-column-end")
          .match(/\d/g)
          .join(""),
        10
      );
      if (gridColumnEnd < gridColumnMax) {
        el.style.gridColumnStart = gridColumnStart + 1;
        el.style.gridColumnEnd = gridColumnEnd + 1;
      }
    }
    function moveUp(el) {
      if (!selected) return;
      var gridRowStart = parseInt(
        window
          .getComputedStyle(el)
          .getPropertyValue("grid-row-start")
          .match(/\d/g)
          .join(""),
        10
      );
      var gridRowEnd = parseInt(
        window
          .getComputedStyle(el)
          .getPropertyValue("grid-row-end")
          .match(/\d/g)
          .join(""),
        10
      );
      if (gridRowStart > 1) {
        el.style.gridRowStart = gridRowStart - 1;
        el.style.gridRowEnd = gridRowEnd - 1;
      }
    }
    function moveDown(el) {
      if (!selected) return;
      var gridRowMax =
        window
          .getComputedStyle(el.parentNode)
          .getPropertyValue("grid-template-rows")
          .split(" ").length + 1;
      var gridRowStart = parseInt(
        window
          .getComputedStyle(el)
          .getPropertyValue("grid-row-start")
          .match(/\d/g)
          .join(""),
        10
      );
      var gridRowEnd = parseInt(
        window
          .getComputedStyle(el)
          .getPropertyValue("grid-row-end")
          .match(/\d/g)
          .join(""),
        10
      );
      if (gridRowEnd < gridRowMax) {
        el.style.gridRowStart = gridRowStart + 1;
        el.style.gridRowEnd = gridRowEnd + 1;
      }
    }
    function rowStartUp(el) {
      var gridRowStart = parseInt(
        window
          .getComputedStyle(el)
          .getPropertyValue("grid-row-start")
          .match(/\d/g)
          .join(""),
        10
      );
      if (gridRowStart > 1) {
        el.style.gridRowStart = gridRowStart - 1;
      }
    }
    function rowStartDown(el) {
      var gridRowStart = parseInt(
        window
          .getComputedStyle(el)
          .getPropertyValue("grid-row-start")
          .match(/\d/g)
          .join(""),
        10
      );
      var gridRowEnd = parseInt(
        window
          .getComputedStyle(el)
          .getPropertyValue("grid-row-end")
          .match(/\d/g)
          .join(""),
        10
      );
      if (gridRowEnd - gridRowStart > 1) {
        el.style.gridRowStart = gridRowStart + 1;
      }
    }
    function rowEndDown(el) {
      var gridRowMax =
        window
          .getComputedStyle(el.parentNode)
          .getPropertyValue("grid-template-rows")
          .split(" ").length + 1;
      var gridRowEnd = parseInt(
        window
          .getComputedStyle(el)
          .getPropertyValue("grid-row-end")
          .match(/\d/g)
          .join(""),
        10
      );
      if (gridRowEnd < gridRowMax) {
        el.style.gridRowEnd = gridRowEnd + 1;
      }
    }
    function rowEndUp(el) {
      var gridRowStart = parseInt(
        window
          .getComputedStyle(el)
          .getPropertyValue("grid-row-start")
          .match(/\d/g)
          .join(""),
        10
      );
      var gridRowEnd = parseInt(
        window
          .getComputedStyle(el)
          .getPropertyValue("grid-row-end")
          .match(/\d/g)
          .join(""),
        10
      );
      if (gridRowEnd - gridRowStart > 1) {
        el.style.gridRowEnd = gridRowEnd - 1;
      }
    }
    function columnStartLeft(el) {
      var gridColumnStart = parseInt(
        window
          .getComputedStyle(el)
          .getPropertyValue("grid-column-start")
          .match(/\d/g)
          .join(""),
        10
      );
      if (gridColumnStart > 1) {
        el.style.gridColumnStart = gridColumnStart - 1;
      }
    }
    function columnStartRight(el) {
      var gridColumnStart = parseInt(
        window
          .getComputedStyle(el)
          .getPropertyValue("grid-column-start")
          .match(/\d/g)
          .join(""),
        10
      );
      var gridColumnEnd = parseInt(
        window
          .getComputedStyle(el)
          .getPropertyValue("grid-column-end")
          .match(/\d/g)
          .join(""),
        10
      );
      if (gridColumnEnd - gridColumnStart > 1) {
        el.style.gridColumnStart = gridColumnStart + 1;
      }
    }
    function columnEndRight(el) {
      var gridColumnMax =
        window
          .getComputedStyle(el.parentNode)
          .getPropertyValue("grid-template-columns")
          .split(" ").length + 1;
      var gridColumnEnd = parseInt(
        window
          .getComputedStyle(el)
          .getPropertyValue("grid-column-end")
          .match(/\d/g)
          .join(""),
        10
      );
      if (gridColumnEnd < gridColumnMax) {
        el.style.gridColumnEnd = gridColumnEnd + 1;
      }
    }
    function columnEndLeft(el) {
      var gridColumnStart = parseInt(
        window
          .getComputedStyle(el)
          .getPropertyValue("grid-column-start")
          .match(/\d/g)
          .join(""),
        10
      );
      var gridColumnEnd = parseInt(
        window
          .getComputedStyle(el)
          .getPropertyValue("grid-column-end")
          .match(/\d/g)
          .join(""),
        10
      );
      if (gridColumnEnd - gridColumnStart > 1) {
        el.style.gridColumnEnd = gridColumnEnd - 1;
      }
    }

    document.onkeydown = function(e) {
      e.preventDefault();
      if (e.ctrlKey && e.which === 38) {
        cssGrid(moveUp);
      } else if (e.ctrlKey && e.which === 40) {
        cssGrid(moveDown);
      } else if (e.ctrlKey && e.which === 37) {
        cssGrid(moveLeft);
      } else if (e.ctrlKey && e.which === 39) {
        cssGrid(moveRight);
      } else if (e.shiftKey && e.which === 38) {
        cssGrid(rowStartUp);
      } else if (e.shiftKey && e.which === 40) {
        cssGrid(rowStartDown);
      } else if (e.shiftKey && e.which === 37) {
        cssGrid(columnStartLeft);
      } else if (e.shiftKey && e.which === 39) {
        cssGrid(columnStartRight);
      } else if (e.altKey && e.which === 38) {
        cssGrid(rowEndUp);
      } else if (e.altKey && e.which === 40) {
        cssGrid(rowEndDown);
      } else if (e.altKey && e.which === 37) {
        cssGrid(columnEndLeft);
      } else if (e.altKey && e.which === 39) {
        cssGrid(columnEndRight);
      } else if (e.shiftKey && e.which === 73) {
        cssGrid(zIndexUp);
      } else if (e.ctrlKey && e.which === 73) {
        cssGrid(zIndexDown);
      } else if (e.ctrlKey && e.which === 67) {
        cssGrid(clone);
      } else if (e.ctrlKey && e.which === 46) {
        cssGrid(remove);
      } else if (e.shiftKey && e.which === 34) {
        cssGrid(selectNext);
      } else if (e.shiftKey && e.which === 33) {
        cssGrid(selectPrevious);
      }
    };
  }
};
</script>

<style>
.bgImg {
  background-image: url("./assets/1000-618.PNG");
  background-size: 100% 100%;
}
</style>
