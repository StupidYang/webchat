<template>
    <div class="clear" :class="[isSelf ? 'right' : 'left']" ref="msg">
        <div class="item">
            <div class="name">
                <span v-if="mytime">{{getdate}}</span> &nbsp;&nbsp;{{name}}
            </div>
            <Avatar
              @click.native="handleClick"
              class="head-place"
              size="small"
              :src="avatar"
              v-flex-touch="handleTouch"
              ></Avatar>
            <div v-if="img">
                <img
                    v-imgSize="pic"
                    alt=""
                    :data-item="isLast && 'last'"
                    class="img"
                    preview-title-enable="true"
                    preview-nav-enable="true">
            </div>
            <span v-if="msg">
                <span v-html="linkMsg" class="msg"></span>
                <!-- {{msg | link}} -->
            </span>
        </div>
    </div>
</template>

<script type="text/ecmascript-6">
import Avatar from "@components/Avatar";
import dateFormat from "../../utils/date";
import { inHTMLData, uriInUnQuotedAttr } from "xss-filters-es6";
export default {
  components: {
    Avatar,
  },
  props: ["id", "name", "img", "msg", "head", "mytime", "is-self", "container", "isNeedScroll", "firstNode", 'isLast'],
  computed: {
    getdate() {
      return dateFormat(new Date(this.mytime), "yyyy-MM-dd HH:mm:ss");
    },
    linkMsg() {
      // 防止xss
      const filterValue = inHTMLData(this.msg);
      return filterValue.replace(
        /(http:\/\/|https:\/\/)((\w|=|\?|\.|\/|&|-)+)/g,
        function($0) {
          const url = $0;
          return `<a style="color: #b374ff" href="${uriInUnQuotedAttr(
            url
          )}" target="_blank">${uriInUnQuotedAttr(url)}</a>`;
        }
      );
    },
    avatar() {
      let avatar = this.head;
      const reg = /\.\/static\/img\/(\d+)\.jpg/;
      const matches = this.head.match(reg);
      if (matches) {
        avatar = `//s3.qiufengh.com/avatar/${matches[1]}.jpeg`;
      }
      return `${avatar}?imageView2/2/w/120/h/120`;
    },
    pic() {
      let pic = this.img;
      if (pic.indexOf("data:image") > -1) {
        return pic;
      }
      return `${pic}?imageView2/2/w/360`;
    }
  },
  mounted() {
    if(this.isLast) {
      this.$refs.msg.scrollIntoView();
    }
  },
  methods: {
    handleClick() {
      console.log(11);
      this.$emit('avatarClick', {
        id: this.name,
      });
    },
    handleTouch(e) {
      console.log(e);
      this.$emit('flexTouch', `@${this.name}，`);
    }
  }
};
</script>
<style lang="stylus" rel="stylesheet/stylus" scoped src="./index.styl"></style>
