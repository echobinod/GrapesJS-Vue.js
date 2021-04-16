<template>
    <div class="app-container">
        <div class="pages-manager">
            <router-link to="dashboard"><mark>DASHBOARD</mark></router-link>
            <div class="add-page" @click="addPage">+ New Page</div>
            <div class="pages">
                <div v-for="page in pages" :key="page.id" :class="{page: 1, selected: isSelected(page) }" @click="selectPage(page.id)">
                    {{ page.get('name') || page.id }} <span v-if="!isSelected(page)" @click="removePage(page.id)" class="page-close">&Cross;</span>
                </div>
            </div>
        </div>
        <div class="editor">
            <div id="gjs"></div>
        </div>
    </div>
</template>

<script>
    import grapesjs from 'grapesjs';
    import 'grapesjs-blocks-basic';

    export default {
        name: 'dashboard',
        data () {
            return {
                editor: null,
                pages: [],
            }
        },
        computed: {
            pm() {
                return this.editor.Pages;
            }
        },
        mounted () {
               this.editor = grapesjs.init({
                container: '#gjs',
                plugins: [
                    'gjs-blocks-basic',
                ],
                pageManager: {
                    pages: 
                    [
                        {
                            id: 'page-1',
                            name: 'Page 1',
                            component: '<div id="comp1">Page 1</div>',
                            styles: `#comp1 { color: red }`,
                        }, 
                        {
                            id: 'page-2',
                            name: 'Page 2',
                            component: '<div id="comp2">Page 2</div>',
                            styles: `#comp2 { color: green }`,
                        }, 
                        {
                            id: 'page-3',
                            name: 'Page 3',
                            component: '<div id="comp3">Page 3</div>',
                            styles: `#comp3 { color: blue }`,
                        }
                    ]
                }
            });

            const { pm } = this;
            this.setPages(pm.getAll());
            this.editor.on('page', () => {
                this.pages = [...pm.getAll()];
            });


        },
        methods: {
            setPages(pages) {
                this.pages = [...pages];
            },
            isSelected(page) {
                return this.pm.getSelected().id == page.id;
            },
            selectPage(pageId) {
                return this.pm.select(pageId);
            },
            removePage(pageId) {
                return this.pm.remove(pageId);
            },
            addPage() {
                const { pm } = this;
                const len = pm.getAll().length;
                pm.add({
                    name: `Page ${len + 1}`,
                    component: `<div>New page (${len +1})</div>`,
                });
            }
        },
    }
</script>

<style>
    body,html {
    height: 100%;
    margin: 0;
  }
  .app-container {
    height: 100%;
    width: 100%;
    display: flex;
  }
  .editor {
    width: 100%;
    height: 100%;
  }
  .pages-manager, .pages {
    display: flex;
    flex-direction: column
  }
  .pages-manager {
      background: #333;
      padding: 5px;
  }
    .add-page {
        background: #444444;
        color: white;
        padding: 5px;
        border-radius: 2px;
        cursor: pointer;
        white-space: nowrap;
        margin-bottom: 10px;
        }
        .page {
        background-color: #444;
        color: white;
        padding: 5px;
        margin-bottom: 5px;
        border-radius: 2px;
        cursor: pointer;
        
        &.selected {
        background-color: #706f6f
        }
    }
  .page-close {
    opacity: 0.5;
    float: right;
    background-color: #2c2c2c;
    height: 20px;
    display: inline-block;
    width: 17px;
    text-align: center;
    border-radius: 3px;
    
    &:hover {
      opacity: 1;
    }
  }
</style>