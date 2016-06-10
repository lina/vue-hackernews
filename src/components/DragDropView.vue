<template>
    <div class="bucket">
      <ul>
          <li 
            v-for="(index, x) in listX" 
            v-draggable:x="{index: index, 
              dragged: 'dragged', listName: 'x'}" 
              v-dropzone:x="{x: sort(listX, index, droptag, dropdata)}"
              v-dropzone:y="{y:move(listY, listX, index, droptag, dropdata), listName: 'x'}" >{{x}}</li>
      </ul>
    </div>
    <div class="bucket">
      <ul>
          <li 
            v-for="(index, y) in listY" 
            v-draggable:y="{index: index, dragged: 'dragged', listName: 'y'}" 
            v-dropzone:y="{y: sort(listY, index, droptag, dropdata)}"
            v-dropzone:x="{x: move(listX, listY, index, droptag, dropdata), listName: 'y'}" >{{y}}</li>
      </ul>
    </div>
    <hr />
</template>

<script>
	import Vue from 'Vue';
  var _, dropTo;


  _ = {
    on: function(el, name, cb) {
      return el.addEventListener(name, cb);
    },
    off: function(el, name, cb) {
      return el.removeEventListener(name, cb);
    }
  };

  dropTo = '';

  Vue.directive('draggable', {
    deep: true,
    params: ['listname'],
  	bind: function() {
      console.log('--------------------------------->draggable->this', this)
  	  this.data = {};
  	  this.dragstart = (function(_this) {
  	    return function(e) {
          console.log("_this inside draggable->bind fn", _this);
          console.log("this inside draggable->bind fn", this);

  	      e.target.classList.add(_this.data.dragged);
  	      e.dataTransfer.effectAllowed = 'all';
  	      e.dataTransfer.dropEffect = 'copy';
  	      e.dataTransfer.setData('data', JSON.stringify(_this.data));
  	      e.dataTransfer.setData('tag', _this.arg);
          console.log('*************************************** -----------------------------------------------> assign dropTo', _this.arg);
  	      dropTo = _this.arg;
  	      console.log('start', _this.arg, _this.data);
  	      return false;
  	    };
  	  })(this);
  	  this.dragend = (function(_this) {
  	    return function(e) {
  	      e.target.classList.remove(_this.data.dragged);
  	      console.log('end', _this.arg);
  	      dropTo = '';
  	      return false;
  	    };
  	  })(this);
  	  this.el.setAttribute('draggable', true);
  	  _.on(this.el, 'dragstart', this.dragstart);
  	  return _.on(this.el, 'dragend', this.dragend);
  	},
  	unbind: function() {
  	  this.el.setAttribute('draggable', false);
  	  _.off(this.el, 'dragstart', this.dragstart);
  	  return _.off(this.el, 'dragend', this.dragend);
  	},
  	update: function(value, old) {
  	  return this.data = value;
  	}
  });


  Vue.directive('dropzone', {
    acceptStatement: true,
    deep: true,
    params: ['listname'],
  	bind: function() {
      console.log('--------------------------------->dropzone->this', this)

      console.log('--------------------------------->dropzone->this.params', this.params)

      console.log('--------------------------------->dropzone->this.params.listname', this.params.listname)

      console.log("dropzone->bind->this", this);

      console.log("dropzone->bind->_this.arg", this.arg);

  	  this.dragenter = (function(_this) {
        console.log('dropzone->dragenter fn-> _this:', _this)
  	    return function(e) {
          console.log("dropzone->dragenter fn-> _this", _this);
          console.log("_this.expression", _this.expression);
          console.log("typeof _this.expression", typeof _this.expression);

          // console.log("JSON.parse(_this.expression)", JSON.parse(_this.expression));
          // console.log('JSON.parse(_this.expression).listName",', JSON.parse(_this.expression).listName)
          // console.log('dropTo', dropTo);
          // var listName = JSON.parse(_this.expression).listName;
  	      if (dropTo === _this.arg) {
  	        // e.target.classList.add(_this.arg);
            e.target.classList.add(_this.arg);

  	      }
  	      // console.log('enter', _this.arg);
  	      return false;
  	    };
  	  })(this);
  	  this.dragleave = (function(_this) {
        console.log('dropzone->dragleave fn-> _this:', _this)

  	    return function(e) {
  	      if (dropTo === _this.arg) {
  	        e.target.classList.remove(_this.arg);
  	      }
  	      console.log('leave', _this.arg);
  	      return false;
  	    };
  	  })(this);
  	  this.drop = (function(_this) {
  	    return function(e) {
  	      var data, tag;
  	      if (e.preventDefault) {
  	        e.preventDefault();
  	      }
  	      data = e.dataTransfer.getData('data');
  	      tag = e.dataTransfer.getData('tag');
  	      if (dropTo === _this.arg) {
  	        _this.handler(tag, JSON.parse(data));
  	        e.target.classList.remove(_this.arg);
  	      }
  	      console.log('drop', _this.arg);
  	      return false;
  	    };
  	  })(this);
  	  this.dragover = (function(_this) {
  	    return function(e) {
  	      if (e.preventDefault) {
  	        e.preventDefault();
  	      }
          console.log('dropzone->this.dragover->dropTo ', dropTo);
          console.log('dropzone->this.dragover->_this.arg ', _this.arg);

  	      if (dropTo === _this.arg) {
  	        e.dataTransfer.effectAllowed = 'all';
  	        e.dataTransfer.dropEffect = 'copy';
  	      } else {
  	        e.dataTransfer.effectAllowed = 'none';
  	        e.dataTransfer.dropEffect = 'link';
  	      }
  	      // console.log('dragover', _this.arg);
  	      return false;
  	    };
  	  })(this);
  	  _.on(this.el, 'dragenter', this.dragenter);
  	  _.on(this.el, 'dragover', this.dragover);
  	  _.on(this.el, 'dragleave', this.dragleave);
  	  return _.on(this.el, 'drop', this.drop);
  	},
  	unbind: function() {
  	  console.log('- dropzone');
  	  _.off(this.el, 'dragenter', this.dragenter);
  	  _.off(this.el, 'dragover', this.dragover);
  	  _.off(this.el, 'dragleave', this.dragleave);
  	  return _.off(this.el, 'drop', this.drop);
  	},
  	update: function(value, old) {
  	  var vm;
  	  vm = this.vm;
  	  return this.handler = function(tag, data) {
  	    var res;

        // console.log('------------>', tag);
  	    vm.droptag = tag;
  	    vm.dropdata = data;
  	    res = value(tag, data);
  	    vm.droptag = null;
  	    vm.dropdata = null;
  	    return res;
  	  };
  	}
  });

  var draggableDirective = Vue.directive('draggable');
  var dropzoneDirective = Vue.directive('dropzone');

  export default {
    // el: '#list',
    data: function() {
      return {
        listX: ['a', 'b', 'c', 'd'],
        listY: ['A', 'B', 'C', 'D']
        // newXData: sort(listX, )
      }
    },
    methods: {
      sort: function(list, id, tag, data) {
        console.log('methods->sort->this', this);
        console.log('methods->sort->_this', +this);

        console.log('methods->sort->list', list);
        console.log('methods->sort->id', id);
        console.log('methods->sort->tag', tag);
        console.log('methods->sort->data', data);
        var tmp;
        console.log('methods->sort->sort', list, id);
        tmp = list[data.index];
        list.splice(data.index, 1);
        var result = list.splice(id, 0, tmp);
        console.log('methods->sort->result', result);
        return result;
      },
      move: function(from, to, id, tag, data) {
        var tmp;
        console.log('methods->move->args', from, to, id, tag, data);
        tmp = from[data.index];
        from.splice(data.index, 1);
        var result = to.splice(id, 0, tmp);
        console.log('methods->move->result', result);
        return result;
      }
    },
    ready: function() {
      console.log('dragdropview.vue ready')
    },
    directives: {
      'draggable': draggableDirective,
      'dropzone': dropzoneDirective
    }
    // components: {
    //   draggableDirective: 
    // }
  };
</script>

<style>
  [draggable] {
      -moz-user-select: none;
      -khtml-user-select: none;
      -webkit-user-select: none;
      user-select: none;
      /* Required to make elements draggable in old WebKit */
      -khtml-user-drag: element;
      -webkit-user-drag: element;
  }
  ul {
      list-style: none;
      padding: 0 0 0 5px;
      margin: 0;
      background: #666;
      width: 200px;
  }
  li {
      box-sizing: border-box;
      border-left: 4px solid transparent;
      padding: 0 0 0 5px;
      color: white;
  }
  li:hover:after {
      color: #c00;
      font-size: x-small;
      content:" (drag me)";
  }
  li.dragged {
      opacity: 0.4;
      color: black;
  }
  li.x {
      border-left: 4px dotted #00c;
  }
  li.y {
      border-left: 4px dotted #0c0;
  }
  li.over-no {
      text-decoration: line-through;
  }

  .bucket {
    display: inline-block;

  }
</style>