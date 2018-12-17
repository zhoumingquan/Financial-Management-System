<template>
  <div class="cloud-sidebar">
    <div class="mian-bar">
      <!-- 展开和隐藏按钮 -->
      <div v-if="!isUnfold" class="narrow-wrapper" @click="changeUnfoldStatus(1)">
        <i class="iconfont icon-zhankaianniuicon narrow-icon"/>
      </div>
      <div v-else class="unfold-wrapper" @click="changeUnfoldStatus(0)">
        <i class="iconfont icon-icon01 unfold-icon"/>
      </div>
      <!-- 展开和隐藏按钮 END -->

      <!-- 一级菜单列表 -->
      <ul class="first-menu-wrapper">
        <li v-for="(unfoldItem,unfoldItemIndex) in menuList" :key="unfoldItemIndex" class="item-menu-wrapper">
          <div class="item-menu-title" @click="unfoldItemMenu(unfoldItem,unfoldItemIndex)" >
            <img v-if="unfoldItemMenuIndex==unfoldItemIndex" src="../assets/img/down.png" alt="">
            <img v-else src="../assets/img/right.png" alt="">
            <span v-if="!isUnfold">{{ unfoldItem.mainTitle }}</span>
          </div>
          <ul v-show="unfoldItemMenuIndex==unfoldItemIndex" :unfoldItemIndex="unfoldItemIndex" class="item-menu-list">
            <li v-for="(mainMenuItem,index) in unfoldItem.submenuList" :key="index" @click="computeSubMenuList(mainMenuItem)">
              <div class="submenu">
                <i :class="mainMenuItem.icon" class="iconfont"/>
                <span v-if="!isUnfold">{{ mainMenuItem.submenuTitle }}</span>
              </div>
            </li>
          </ul>
        </li>
      </ul>
      <!-- 一级菜单列表 END -->
    </div>
    <div class="sub-bar">
      <div v-if="!isHideSubMenu" class="sub-fold-wrapper" @click="hideSubMenu">
        <i class="iconfont icon-meunfold sub-fold-icon" />
      </div>
      <div v-else class="sub-unfold-wrapper" @click="hideSubMenu">
        <i class="iconfont icon-menuunfold sub-unfold-icon"/>
      </div>
      <div class="sub-header-title">{{ submenuTitle }}</div>
      <div class="sub-menu-list">
        <router-link v-for="(submenuItem,index) in subMenuList" :to="submenuItem.path" :key="index" class="sub-menu-item" active-class="sub-menu-item-active">{{ submenuItem.submenuItemTitle }}</router-link>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      isUnfold: false, // 判断展开还是隐藏一级菜单,
      unfoldItemMenuIndex: 0, // 判断展开还是隐藏一个菜单列表
      isHideSubMenu: false,
      isShowMainMenu: false,
      menuArr: [
        {
          type: 1,
          menuList: [
            {
              main_memu_id: 0,
              mainTitle: '云计算基础服务',
              submenuList: [
                {
                  submenuTitle: '云服务ECS',
                  icon: 'icon-yunguanjia',
                  submenuID: 1,
                  submenuItemList: [
                    {
                      submenuItemTitle: '概况',
                      path: '/cloud/page1'
                    },
                    {
                      submenuItemTitle: '实例',
                      path: '/cloud/page2'
                    }
                  ]
                },
                {
                  submenuTitle: '域名',
                  icon: 'icon-yuming',
                  submenuID: 2,
                  submenuItemList: [
                    {
                      submenuItemTitle: '域名列表',
                      path: '/cloud/page3'
                    },
                    {
                      submenuItemTitle: '信息模板',
                      path: '/cloud/page4'
                    }
                  ]
                }
              ]
            },
            {
              main_memu_id: 1,
              mainTitle: '大数据',
              submenuList: [
                {
                  submenuTitle: '机器学习',
                  submenuID: 3,
                  icon: 'icon-xuexi',
                  submenuItemList: [
                    {
                      submenuItemTitle: '记忆学习',
                      path: '/cloud/page5'
                    },
                    {
                      submenuItemTitle: '深度学习',
                      path: '/cloud/page6'
                    }
                  ]
                },
                {
                  submenuTitle: '数据集成',
                  submenuID: 4,
                  icon: 'icon-shuju',
                  submenuItemList: [
                    {
                      submenuItemTitle: '大数据分析',
                      path: '/cloud/page7'
                    },
                    {
                      submenuItemTitle: '统计图',
                      path: '/cloud/page8'
                    }
                  ]
                }
              ]
            }
          ]
        }
      ],
      subMenuList: [],
      submenuTitle: '',
      menuList: []
    }
  },
  watch: {
    $route (to, from) {
      this.menuList.map(v => {
        v.submenuList.map(vm => {
          vm.submenuItemList.map(cm => {
            if (cm.path === to.path) {
              this.submenuTitle = vm.submenuTitle
              this.unfoldItemMenuIndex = v.main_memu_id
              const submenuObj = document.getElementsByClassName('submenu')
              for (let i = 0; i < submenuObj.length; i++) {
                submenuObj[i].className = 'submenu'
              }
              submenuObj[vm.submenuID - 1].className = 'submenu submenu-active'
              sessionStorage.setItem('submenuID', vm.submenuID - 1)
              sessionStorage.setItem('unfoldItemMenuIndex', v.main_memu_id)
              sessionStorage.setItem('submenuTitle', vm.submenuTitle)
              this.menuList.map(v => {
                v.submenuList.map(am => {
                  if (am.submenuID === vm.submenuID) {
                    this.subMenuList = am.submenuItemList
                  }
                })
              })
            }
          })
        })
      })
    }
  },
  created () {
    // 通过不同权限显示不同页面
    const type = 1
    this.menuArr.map(v => {
      if (v.type === type) {
        this.menuList = v.menuList
      }
    })
  },
  mounted () {
    if (sessionStorage.getItem('isUnfold')) {
      if (sessionStorage.getItem('isUnfold') === 1) {
        this.isUnfold = true
      } else {
        this.isUnfold = false
      }
    }
    this.activateSubmenu()
    if (sessionStorage.getItem('mainMenuItemIndex')) {
      const index = Number(sessionStorage.getItem('mainMenuItemIndex'))
      const submenuObj = document.getElementsByClassName('submenu')
      submenuObj[index].className = 'submenu submenu-active'
      this.unfoldItemMenuIndex = submenuObj[
        index
      ].parentNode.parentNode.getAttribute('unfoldItemIndex')
    }
    if (sessionStorage.getItem('submenuID')) {
      const submenuObj = document.getElementsByClassName('submenu')
      for (let i = 0; i < submenuObj.length; i++) {
        submenuObj[i].className = 'submenu'
      }
      const index = Number(sessionStorage.getItem('submenuID'))
      submenuObj[index].className = 'submenu submenu-active'
      this.menuList.map(v => {
        v.submenuList.map(vm => {
          if (vm.submenuID === Number(sessionStorage.getItem('submenuID')) + 1) {
            this.subMenuList = vm.submenuItemList
          }
        })
      })
    }
    if (sessionStorage.getItem('submenuTitle')) {
      this.submenuTitle = sessionStorage.getItem('submenuTitle')
    }
  },
  methods: {
    changeUnfoldStatus (code) {
      if (code === 1) {
        this.isUnfold = true
        sessionStorage.setItem('isUnfold', '1')
      } else {
        this.isUnfold = false
        sessionStorage.setItem('isUnfold', '0')
      }
    },
    unfoldItemMenu (unfoldItem, unfoldItemIndex) {
      if (this.unfoldItemMenuIndex === unfoldItemIndex) {
        this.unfoldItemMenuIndex = null
      } else {
        this.unfoldItemMenuIndex = unfoldItemIndex
      }
      sessionStorage.setItem('unfoldItemMenuIndex', unfoldItemIndex)
    },
    hideSubMenu () {
      const subbarObj = document.getElementsByClassName('sub-bar')[0]
      if (this.isHideSubMenu) {
        this.isHideSubMenu = false
        subbarObj.style.width = '180px'
      } else {
        this.isHideSubMenu = true
        subbarObj.style.width = '0px'
      }
    },
    activateSubmenu () {
      const submenuObj = document.getElementsByClassName('submenu')
      for (let i = 0; i < submenuObj.length; i++) {
        submenuObj[i].onclick = function () {
          for (let j = 0; j < submenuObj.length; j++) {
            submenuObj[j].className = 'submenu'
          }
          submenuObj[i].className = 'submenu submenu-active'
          sessionStorage.setItem('mainMenuItemIndex', i)
        }
      }
    },
    computeSubMenuList (info) {
      sessionStorage.setItem('submenuID', info.submenuID)
      this.subMenuList = info.submenuItemList
      this.submenuTitle = info.submenuTitle
      this.$router.push({ path: info.submenuItemList[0].path })
    }
  }
}
</script>

<style lang='scss'>
.cloud-sidebar {
  height: 100%;
  .mian-bar {
    float: left;
    height: 100%;
    background: #333744;
    .narrow-wrapper {
      width: 180px;
      height: 30px;
      background: #4a5064;
      display: flex;
      align-items: center;
      justify-content: center;
      cursor: pointer;
      .narrow-icon {
        font-size: 12px;
        color: #aeafa7;
      }
    }
    .narrow-wrapper:hover .narrow-icon {
      color: #fff;
    }
    .unfold-wrapper {
      width: 50px;
      height: 30px;
      background: #4a5064;
      display: flex;
      align-items: center;
      justify-content: center;
      cursor: pointer;
      .unfold-icon {
        font-size: 20px;
        color: #aeafa7;
      }
    }
    .unfold-wrapper:hover .unfold-icon {
      color: #fff;
    }
    .first-menu-wrapper {
      color: #fff;
      font-size: 12px;
      .item-menu-wrapper {
        .item-menu-title {
          display: flex;
          height: 40px;
          align-items: center;
          background: #42485b;
          cursor: pointer;
          img {
            margin-left: 18px;
            margin-right: 8px;
          }
        }
        .item-menu-title:hover {
          background: #00c1de;
        }
        .item-menu-list {
          .submenu {
            color: #aeb9c2;
            height: 40px;
            line-height: 40px;
            cursor: pointer;
            i {
              display: inline-block;
              width: 16px;
              height: 16px;
              margin: 0 17px;
            }
          }
          .submenu:hover {
            background: #4a5064;
          }
          .submenu-active {
            background: #00c1de;
            color: #fff;
          }
          .submenu-active:hover {
            background: #00c1de;
            color: #fff;
          }
        }
      }
    }
  }
  .sub-bar {
    float: left;
    width: 180px;
    height: 100%;
    transition: width 0.2s;
    background: #eaedf1;
    position: relative;
    .sub-fold-wrapper {
      width: 20px;
      height: 50px;
      line-height: 50px;
      text-align: center;
      background: #f7f7f7;
      cursor: pointer;
      position: absolute;
      top: 0;
      right: 0;
      bottom: 0;
      z-index: 2;
      margin: auto 0;
      .sub-fold-icon {
        font-size: 16px;
        color: #546478;
      }
    }
    .sub-unfold-wrapper {
      width: 20px;
      height: 50px;
      line-height: 50px;
      text-align: center;
      background: #d9dee4;
      cursor: pointer;
      position: absolute;
      top: 0;
      left: 0;
      bottom: 0;
      z-index: 2;
      margin: auto 0;
      .sub-unfold-icon {
        font-size: 16px;
        color: #546478;
      }
    }
    .sub-header-title {
      height: 70px;
      line-height: 70px;
      background: #d9dee4;
      overflow: hidden;
      text-overflow: ellipsis;
      white-space: nowrap;
      font-weight: 600;
      font-size: 12px;
      text-indent: 20px;
      box-sizing: border-box;
    }
    .sub-menu-list {
      position: absolute;
      width: 100%;
      height: 100%;
      top: 0;
      left: 0;
      padding-top: 70px;
      box-sizing: border-box;
      overflow-y: auto;
      .sub-menu-item {
        display: block;
        height: 40px;
        line-height: 40px;
        overflow: hidden;
        text-overflow: ellipsis;
        white-space: nowrap;
        text-indent: 20px;
        color: #333;
        font-size: 12px;
      }
      .sub-menu-item:hover {
        background: #f4f6f8;
      }
      .sub-menu-item-active {
        background: #fff;
      }
      .sub-menu-item-active:hover {
        background: #fff;
      }
    }
  }
}
</style>
