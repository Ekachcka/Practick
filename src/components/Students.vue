<template>

    <div>
        <table>
            <input placeholder="Пошук..." type="text" v-model="query">
            <tr>
                <th>ПІБ</th>
                <th>Група</th>
                <th>Оцінка</th>
                <th>Здав/не здав</th>
            </tr>
            <tr v-for="student in students" :key="student._id" :class="(student.name.split(' ')[0].includes(query) && query) ? 'red' : null">
                
                    <td>
                        <router-link v-bind:to="'/student-info/'+student._id">
                        {{student.name}}
                        </router-link>
                    </td>
                    
                    <td>
                        {{student.group}}
                    </td>
                    <td>
                        {{student.mark}}
                    </td>
                    <td>
                        <input type="checkbox" id="checkbox" :checked="student.isDonePr" />
                    </td>
                    <td>
                        <a @click="deleteStudent(student._id)">Видалити</a>
                    </td>
                    <td>
                        <button @click="initUpdateForm(student._id)"><img class="image" src="" alt="Редагувати"></button>
                    </td>
            </tr>
        </table>

        <input placeholder="ПІБ" type="text" v-model.trim="studentForm.name">
        <select v-model="studentForm.group">
            <option disabled value="">Група</option>
            <option>RPZ 18 1/9</option>
            <option>RPZ 18 2/9</option>
        </select> 
        <input placeholder="Оцінка" type="number" step="1" min="1" v-model.number="studentForm.mark">
        <input type="checkbox" id="passed" value="false" v-model="studentForm.isDonePr">
        <label for="passed">Здав ПР</label>
        <button @click="addStudent">ок</button> 
        <button @click="updateStudent(studentForm._id)" :class="isButtonVisible ? 'visible': 'not-visible'" >update</button> 
    </div>

</template>

<script>
import Vue from "vue";

export default {
       props: {
           id: '',
       },


    data: function(){ 
            return{
                    students: [],
                    query: '',
                    studentForm: {
                        name: '',
                        group: '',
                        mark: null,
                        isDonePr: false,
                    },
                    isButtonVisible: false
                }
            },
        mounted: function(){
            Vue.axios.get("http://46.101.212.195:3000/students").then(response => {
                this.students = [...response.data];
            })
            
        },
        methods: {
            deleteStudent: function(id){
                Vue.axios.delete(`http://46.101.212.195:3000/students/${id}`)
                .then(()=>{this.students = this.students.filter(student=>student._id !== id)})
            },
            initUpdateForm: function(id){
                
                this.isButtonVisible = true;
                const currentStudent = this.students.find(student => student._id === id)
                this.studentForm = {...currentStudent}
                
            },
            addStudent: function(){
                if(Object.values(this.studentForm).every(value => value === false ? true : value)){
                    Vue.axios.post("http://46.101.212.195:3000/students", this.studentForm)
                    .then(response=> this.students.push(response.data))
                } else{
                    alert('Введіть всі данні')
                }
                
            },
            updateStudent: function(id){
                this.isButtonVisible = false;
                const updatedStudent = {...this.studentForm}
                Vue.axios.put(`http://46.101.212.195:3000/students/${id}`, updatedStudent)
                .then(()=>{this.students = this.students.map(student=>student._id === id ? updatedStudent : student)})
            }
        }, 
}
</script>

<style scoped>
</style>