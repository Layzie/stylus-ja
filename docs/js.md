 + ������: [stylus/docs/js.md at 0ab9219d80a5304e32437ef3cabb7b3fa1345534 ? LearnBoost/stylus ? GitHub](https://github.com/LearnBoost/stylus/blob/0ab9219d80a5304e32437ef3cabb7b3fa1345534/docs/js.md "stylus/docs/js.md at 0ab9219d80a5304e32437ef3cabb7b3fa1345534 ? LearnBoost/stylus ? GitHub")

## JavaScript API [����](http://learnboost.github.com/stylus/docs/js.html)

Simply `require` the module, and call `render()` with the given string of Stylus code, and (optional) `options` object. 

���W���[���� `require` ����`render()` �ŌĂяo���܂��B�����̕�����ɂ�Stylus�R�[�h�A�܂��A�C�ӂ� `options` �I�u�W�F�N�g��n���܂��B

Frameworks utilizing Stylus should pass the `filename` option to provide better error reporting.

Stylus�𗘗p�����t���[�����[�N�ɂ����āA���ǂ��G���[���|�[�g��񋟂��邽�߂ɂ� `filename` �I�v�V������n���K�v������܂��B

    var stylus = require('stylus');

    stylus.render(str, { filename: 'nesting.css' }, function(err, css){
      if (err) throw err;
      console.log(css);
    });

We can also do the same thing in a more progressive manner:

��������i�񂾕��@�ōs���Ǝ��̂悤�ɂȂ�܂��B

    var stylus = require('stylus');

    stylus(str)
      .set('filename', 'nesting.css')
      .render(function(err, css){
        // logic
      });

### .set(setting, value)

 Apply a setting such as a `filename`, or import `paths`:

 `filename` ��C���|�[�g���� `paths` �̐ݒ�Ȃǂ�K�p���܂��B
 
     .set('filename', __dirname + '/test.styl')
     .set('paths', [__dirname, __dirname + '/mixins'])

### .include(path)

  A progressive alternative to `.set('paths',...)` is `.include()`.  This is ideal when exposing external Stylus libraries which expose a path.

  `.set('paths',...)` ����������i�񂾕��@�Œu���������ꍇ�A `.include()` �ƂȂ�܂��B����́A�ˑ�����O����Stylus���C�u�����ɂ��Ẵp�X�𖾎�����ꍇ�Ɏg�p���邱�Ƃ����z�I�ł��B

    stylus(str)
      .include(require('nib').path)
      .include(process.env.HOME + '/mixins')
      .render(...)

### .import(path)

Defer importing of the given `path` until evaluation is performed. The example below is essentially the same as doing `@import 'mixins/vendor'` within your Stylus sheet.

`path` �œn���ꂽ�Ώۂ́A�]�����J�n�����܂ŃC���|�[�g���x������܂��B���̗�́A�X�^�C���V�[�g���ōs���Ă��邱�Ƃ� `@import 'mixins/vendor'` �Ɗ�{�I�ɓ����ł��B

      var stylus = require('../')
        , str = require('fs').readFileSync(__dirname + '/test.styl', 'utf8');

      stylus(str)
        .set('filename', __dirname + '/test.styl')
        .import('mixins/vendor')
        .render(function(err, css){
        if (err) throw err;
        console.log(css);
      });

### .define(name, node)

 By passing a `Node`, we may define a global variable. This is useful when exposing conditional features within your library depending on the availability of another. For example the **Nib** extension library conditionally supports node-canvas, providing image generation. 

 `Node` �ɓn�����ƂŁA�O���[�o���ϐ����`���邱�Ƃ��ł��܂� �B����́A�O������̏����iNode�ɓn�����O���[�o���ϐ��j�ɉ����āA���C�u�����̒��ŏ����t���@�\�����J����ꍇ�ɗ��p���܂��B �Ⴆ�΁A**Nib** �̉摜�����@�\��񋟂���g�����C�u�����́Anode-canvas�̏����ɂ��T�|�[�g���܂��B
 
 However, this is not always available, so Nib may define:
 
 �������A����͏�ɗ��p�ł���Ƃ͌���܂���B������Nib�͂��̂悤�ɒ�`���܂��B
 
     .define('has-canvas', stylus.nodes.false);
     .define('some-setting', new stylus.nodes.String('some value'));

 Stylus also casts JavaScript values to their Stylus equivalents when possible. Here are a few examples:

 �\�ȏꍇ�AStylus�ł�Javascript�̒l��Stylus�ł̓����Ȓl�ɃL���X�g���܂��B�����Ɋ���̗�������܂��B

     .define('string', 'some string')
     .define('number', 15.5)
     .define('some-bool', true)
     .define('list', [1,2,3])
     .define('list', [1,2,[3,4,[5,6]]])
     .define('list', { foo: 'bar', bar: 'baz' })
     .define('families', ['Helvetica Neue', 'Helvetica', 'sans-serif'])

  These same rules apply to return values in js functions as well:

  �����Ɠ������[����JS�֐��i�R�[���o�b�N�j�̖߂�l�ɂ��K�p����܂��B

     .define('get-list', function(){
       return ['foo', 'bar', 'baz'];
     })

### .define(name, fn)

 This method allows you to provide a JavaScript-defined function to Stylus. Think of these as you would JavaScript-to-C++ bindings. When there's something you cannot do in Stylus, define it in JavaScript!

 ���̕��@�ɂ��AStylus��Javascript�Œ�`���ꂽ�֐���񋟂��邱�Ƃ��ł��܂��B����́AJavascript����C++�ւ̃o�C���f�B���O�̂悤�ɍl���邱�Ƃ��ł��܂��BStylus�ɂčs�����Ƃ��ł��Ȃ�����������ꍇ�AJavascript�ł�����`����I

In this example, we define four functions: `add()`, `sub()`, `image-width()`, and `image-height()`. These functions must return a `Node`, this constructor and the other nodes are available via `stylus.nodes`.

���̗�ł́A`add()`, `sub()`, `image-width()`, `image-height()` ��4�̊֐����`���Ă��܂��B�����̊֐���`Node`��Ԃ��K�v������܂��B

      var stylus = require('../')
        , nodes = stylus.nodes
        , utils = stylus.utils
        , fs = require('fs');

      function add(a, b) {
        return a.operate('+', b);
      }

      function sub(a, b) {
        return a.operate('-', b);
      }

      function imageDimensions(img) {
        // assert that the node (img) is a String node, passing
        // the param name for error reporting
        utils.assertType(img, 'string', 'img');
        var path = img.val;

        // Grab bytes necessary to retrieve dimensions.
        // if this was real you would do this per format,
        // instead of reading the entire image :)
        var data = fs.readFileSync(__dirname + '/' + path);

        // GIF
        // of course you would support.. more :)
        if ('GIF' == data.slice(0, 3).toString()) {
          var w = data.slice(6, 8)
            , h = data.slice(8, 10);
          w = w[1] << 8 | w[0];
          h = h[1] << 8 | h[0];
        }

        return [w, h];
      }

      function imageWidth(img) {
        return new nodes.Unit(imageDimensions(img)[0]);
      }

      function imageHeight(img) {
        return new nodes.Unit(imageDimensions(img)[1]);
      }

      stylus(str)
        .set('filename', 'js-functions.styl')
        .define('add', add)
        .define('sub', sub)
        .define('image-width', imageWidth)
        .define('image-height', imageHeight)
        .render(function(err, css){
          if (err) throw err;
          console.log(css);
        });

 For further reference (until documentation is complete) please see the following files:

 �i�h�L�������g����������܂ł́j�Q�l�Ɉȉ��̃t�@�C�����Q�Ƃ��Ă��������B
 
   - `lib/nodes/*`
   - `lib/utils.js`

### .use(fn)

  When called, the given `fn` is invoked with the renderer, allowing all of the methods above to be used. This allows for plugins to easily expose themselves, defining functions, paths etc.

  �n���ꂽ `fn` �́Arender()�ŌĂяo����܂��B�Ăяo���ꂽ�ꍇ�A��L���ׂĂ�API�֐��𗘗p���邱�Ƃ��ł��܂��B����ɂ��A�v���O�C������`���Ă���֐��A�p�X�ȂǁA�������g���ȒP�Ɍ��J���邱�Ƃ��ł��܂��B

    var mylib = function(style){
      style.define('add', add);
      style.define('sub', sub);
    };

    stylus(str)
      .use(mylib)
      .render(...)
