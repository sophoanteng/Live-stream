<script>
export default {
  globalData: {
    userinfo: [],
    site_url:'http://dv.seokmcsowmsk.com', //"http://dv.seokmcsowmsk.com"
		socket_url: "http://34.142.153.212:19967", //35.241.92.99 //"http://dv.seokmcsowmsk.com:19967"
  },
  onLaunch: function (e) {
    //是否登录判断
    global.isLogin = function () {
      // var uid = uni.getStorageSync("uid");
      // var token = uni.getStorageSync("token");
      var uid = 45639;
      var token = "f7be04f77b376cd032e648bef5b608f3";
      if (uid == "" || token == "") {
        return false;
      } else {
        //判断token是否有效
        uni.request({
          url: "http://dv.seokmcsowmsk.com/appapi/?service=User.iftoken", //"http://dv.seokmcsowmsk.com/appapi/?service=User.iftoken"
          method: "GET",
          data: {
            uid: uid,
            token: token,
          },
          success: (res) => {
            var data = res.data.data;

            if (data["code"] != 0) {
              let sysInfo = uni.getSystemInfoSync();
              uni.showModal({
                title: "请登录",
                content: data["msg"],
                success: function () {
                  console.log("windowWidth:" + sysInfo.windowWidth);
                  if (sysInfo.windowWidth > 600) {
                    uni.$emit("showLoginBox", {});
                    return 1;
                  } else {
                    uni.navigateTo({
                      url: "/pages/login/login",
                    });
                  }
                },
              });
              uni.removeStorageSync("uid");
              uni.removeStorageSync("token");
              return false;
            }
          },
        });
        return [uid, token];
      }
    };
    console.log("App Launch");
  },
  onShow: function () {
    console.log("App Show");
  },
  onHide: function () {
    console.log("App Hide");
  },
};
</script>

<style>
/*每个页面公共css */
@import "./common/uni.css";
@import "common/app.css";

@import "colorui/main.css";
@import "colorui/icon.css";
</style>
