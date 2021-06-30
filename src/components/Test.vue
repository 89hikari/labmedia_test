<template>
    <div class="container">
        <div>
            <h1 class="title">Список пользователей</h1>
            <div class="input-block">
                <input class="input-block-input" type="text" placeholder="Поиск по имени или e-mail" v-model="searchText" @input="searchText = $event.target.value">
                <div v-if="searchText != '' || sorted">
                    <button class="input-clear" @click="clearSort" >Очистить фильтр</button>
                </div>
            </div>
        </div>
        <div class="data-block">
            <div class="sort">
                <p>Сортировка:</p>
                <button @click="sortByDate(); isActive1 = true; isActive2 = false" :class="{ active: isActive1 }">Дата регистрации</button>
                <button @click="sortByRating(); isActive2 = true; isActive1 = false" :class="{ active: isActive2 }">Рейтинг</button>
            </div>
            <div class="data-container">
                    <table class="data-container-table">
                        <tr>
                            <th>Имя пользователя</th>
                            <th>E-mail</th>
                            <th>Дата регистрации</th>
                            <th>Рейтинг</th>
                            <th></th>
                        </tr>
                        <tr v-for="(user, index) in getFilteredNames" :key="index">
                            <td>{{user.username}}</td>
                            <td>{{user.email}}</td>
                            <td>{{getFormattedDate(user.registration_date)}}</td>
                            <td>{{user.rating}}</td>
                            <td><button class="remove-button" type="button" @click="showModal(index)"></button></td>
                        </tr>
                    </table>
                    <Popup :del="deleteItem" :ind="getFilteredNames[forDelete]"
                                v-show="isModalVisible"
                                @close="closeModal"
                            />
            </div>
            <div v-for="(user, index) in Math.ceil(users.filter(item => item.username.toLowerCase().includes(searchText.toLowerCase()) || item.email.toLowerCase().includes(searchText.toLowerCase())).length / 5)" :key="index" class="pagination">
                <button @click="page = index; start = index * 5">{{index+1}}</button>
            </div>
        </div>
    </div>
</template>

<script>

import axios from 'axios';
import Popup from './Popup.vue';
import './../assets/styles/main.css';

export default {
    name: 'Test',
    components: {
      Popup, 
    },
    data() {
        return {
            users: [],
            loading: true,
            errored: false,
            searchText: '',
            isActive1: false,
            isActive2: false,
            sorted: false,
            sortByRatingPressed: false,
            sortByDatePressed: false,
            page: 0,
            start: 0,
            total: this.getTotal,
            isModalVisible: false,
            forDelete: null
        }
    },
    methods: {
        getFormattedDate(date){
            return date.slice(8,10) + '.' + date.slice(5,7) + '.' + date.slice(0,4);
        },
        showModal(index) {
            this.forDelete = index;
            this.isModalVisible = true;
        },
        closeModal() {
            this.isModalVisible = false;
            this.forDelete = null;
        },
        sortByRating(){
            this.sorted = true
            let result = this.users;
            if(this.sortByRatingPressed) {
                this.sortByRatingPressed = false
                return result.sort(function(a, b) {
                    return a.rating-b.rating
                })
            } else {
                this.sortByRatingPressed = true
                return result.sort(function(a, b) {
                    return b.rating-a.rating
                })
            }
        },
        getLength () {
            return this.users.length;
        },
        getTotal () {
            return Math.ceil(this.getLength() / 5)
        },
        sortByDate(){
            this.sorted = true
            if(this.sortByDatePressed){
                this.sortByDatePressed = false
                return this.users.sort(function(a, b) {
                    return new Date(a.registration_date) - new Date(b.registration_date);
            })
            } else {
                this.sortByDatePressed = true
                return this.users.sort(function(a, b) {
                    return new Date(b.registration_date) - new Date(a.registration_date);
            })
            }
        },
        deleteItem(index) {
            this.users = this.users.filter(item => !(item.id == index.id));
        },
        clearSort(){
            let result = this.users;
            this.sorted = false;
            this.isActive1 = false;
            this.isActive2 = false;
            this.searchText = '';
            return result.sort(function(a, b) {
                return a.id-b.id
            })
        },
        // переход на 1 страницу если что-то введено в поиске
        checkIfSearchNotEmpty() {
            this.searchText != '' ? this.page = 0 : false;
        }
    },

    computed: {
        getFilteredNames() {
            let result = this.users;
            if(this.searchText) {
                result = result.filter(item => item.username.toLowerCase().includes(this.searchText.toLowerCase()) || item.email.toLowerCase().includes(this.searchText.toLowerCase()));
            }
            return result.slice(this.start, this.start + 5);
        },
    },
    
    mounted() {
        axios
        .get('https://5ebbb8e5f2cfeb001697d05c.mockapi.io/users')
            .then(response => {
                this.users = response.data;
            })
            .catch(error => {
                console.log(error);
                this.errored = true;
            })
            .finally(() => (this.loading = false));
    }
}
</script>

<style lang="less" scoped>

    .container{
        padding: 20px 10px;
        font-family: 'Zona Pro';
        max-width: 1200px;
        margin: 0 auto;
        .title{
            margin-bottom: 15px;
        }
        .input-block{
            border-radius: 15px;
            background-color: #fff;
            padding: 20px 50px 20px 20px;
            -webkit-box-shadow: 0px 36px 26px -12px rgba(34, 60, 80, 0.14);
            -moz-box-shadow: 0px 36px 26px -12px rgba(34, 60, 80, 0.14);
            box-shadow: 0px 36px 26px -12px rgba(34, 60, 80, 0.14);
            margin-bottom: 100px;
            &-input{
                font-size: 16px;
                display: block;
                border: none;
                font-family: 'Zona Pro';
                background-color: rgba(216, 216, 216, 0.5);
                border-radius: 15px;
                width: calc(100% - 50px);
                padding: 14px 14px 14px 50px;
                margin-bottom: 30px;
                background-image: url('./../assets/images/loupe.png');
                background-repeat: no-repeat;
                background-position: left center;
                background-size: 22px;
                background-position-x: 15px;
                &:focus{
                    outline: none;
                }
                
            }
            .input-clear{
                font-family: 'Zona Pro';
                display: inline-block;
                border: none;
                background-color: #fff;
                font-size: 16px;
                cursor: pointer;
                text-align: center;
                padding: 6px 0px 6px 30px;
                background-image: url('./../assets/images/broom.png');
                background-repeat: no-repeat;
                background-position: left center;
                background-size: 20px;
                background-position-x: 0;
            }
        }

        .data-block{
            .pagination{
                margin-top: 15px;
                & > * {
                    float: left;
                    display: block;
                    border: none;
                    font-family: 'Zona Pro';
                    font-size: 18px;
                    cursor: pointer;
                    width: 35px;
                    background-color: rgb(162, 221, 255);
                    height: 35px;
                    margin-right: 10px;
                    border-radius: 50%;
                }
            }
            .sort{
                display: flex;
                flex-direction: row;
                margin-bottom: 20px;
                & > * {
                    opacity: 0.7;
                    font-size: 14px;
                    margin-right: 10px;
                    font-family: 'Zona Pro';
                }
                & > button{
                    display: inline-block;
                    border: none;
                    background: rgb(245, 245, 245);
                    cursor: pointer;
                    border-bottom: 1px dashed black;
                }
                & > button.active{
                    opacity: 1 !important;
                }
            }
            .data-container{
                background-color: #fff;
                border-radius: 15px;
                padding: 0px 25px 20px 25px;
                &-table{
                    width: 100%;
                    text-align: left;
                    & td, th{
                        text-align: left;
                        padding: 20px 0;
                        font-weight: normal;
                        border-bottom: 1px solid rgba(185, 185, 185, 0.5);
                    }
                    & th{
                        font-size: 14px;
                    }

                    & td:nth-child(1) {
                        color: rgb(0, 162, 255);
                        font-weight: bolder;
                    }
                    .remove-button {
                        font-family: 'Zona Pro';
                        display: inline-block;
                        border: none;
                        background-color: #fff;
                        font-size: 16px;
                        cursor: pointer;
                        text-align: center;
                        padding: 6px 0px 6px 30px;
                        background-image: url('./../assets/images/remove.svg');
                        background-repeat: no-repeat;
                        background-position: center center;
                        background-size: 20px;
                    }
                }
            }
        }
    }

</style>