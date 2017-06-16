# ajax 触发时的特效，提示请稍等
JS
```bash
$(function() {
  // ajax loading image
  $('.loading').hide();
  $('.loading_tip').hide();
  $(document).ajaxStart(function() {
    $('.loading').fadeIn();
    $('.loading_tip').fadeIn();
  });
  $(document).ajaxStop(function() {
    $('.loading').fadeOut();
    $('.loading_tip').fadeOut();
  });
});
```
HTML
```bash
<div class="loading"></div>
<div class="loading_tip">请稍等...</div>
```
CSS
```bash
.loading {
  background-color: black;
  opacity: 0.4;
  position: fixed;
  top: 0;
  left: 0;
  height: 100%;
  width: 100%;
  display: none;
  z-index: 100;
}

.loading_tip {
  display: none;
  background-color: black;
  border-radius: 10px;
  color: white;
  left: 0;
  text-align: center;
  margin: auto;
  opacity: 0.7;
  padding: 2% 5%;
  position: fixed;
  right: 0;
  top: 50%;
  width: 30%;
  z-index: 200;
}
```