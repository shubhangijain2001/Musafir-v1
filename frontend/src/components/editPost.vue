<template>
    <div class="popup" @click="closePopup">
  
      <!-- To prevent the form from closing when you click inside it, 
      add an event listener (.stop) and stop the event propagation. 
      This will stop the click event from bubbling up to the .popup element and triggering the closePopup method. -->
  
      <div class="form-container" @click.stop>
  
        <h1>Edit Post</h1>
        <div class="divider"></div>
  
        <div class="profile">
          <img src="https://i.ibb.co/wJGzYTt/PFP.png" alt="Profile Icon">
          <div class="username-category">
            <p class="username">{{ username }}</p>
            <select id="category" v-model="category">
              <option id="default" hidden selected value="">Choose Category</option>
              <option value="Historical">Historical</option>
              <option value="Gardens">Gardens</option>
              <option value="Worship Places">Worship Places</option>
              <option value="Adventure">Adventure</option>
              <option value="Food & Beverages">Food & Beverages</option>
              <option value="Others">Others</option>
            </select>
          </div>
        </div>
  
        <textarea v-model="description" placeholder="Been exploring? Share your experience now! (No &apos; or &quot; allowed in the text box :3)"></textarea>
  
  
        <div class="input-row">
          <input type="text" v-model="place" placeholder="Enter the Place">
          <input type="text" v-model="city" placeholder="Enter the City">
          <input type="text" v-model="state" placeholder="Enter the State">
        </div>
  
        <!-- <label for="upload-images" class="upload-btn">
  
          Add images to your post<i class="fas fa-image"></i>
        </label>
        <input type="file" id="upload-images" name="avatar" @change="handleFileChange" multiple style="display: none;"> -->
  
  
  
        <div class="rating-row">
          <!-- <label for="">Value for money</label> -->
          <select id="value_for_money" v-model="value_for_money">
            <option id="default" hidden selected value="">Value for Money</option>
            <option value="1">1</option>
            <option value="2">2</option>
            <option value="3">3</option>
            <option value="4">4</option>
            <option value="5">5</option>
          </select>
  
          <!-- <label for="">Safety & Security</label> -->
          <select id="safety" v-model="safety">
            <option id="default" hidden selected value="">Safety & Security</option>
            <option value=1>1</option>
            <option value=2>2</option>
            <option value=3>3</option>
            <option value=4>4</option>
            <option value=5>5</option>
          </select>
  
          <!-- <label for="">Overall Experience</label> -->
          <select id="overall_exp" v-model="overall_exp">
            <option id="default" hidden selected value="">Overall Experience</option>
            <option value=1>1</option>
            <option value=2>2</option>
            <option value=3>3</option>
            <option value=4>4</option>
            <option value=5>5</option>
          </select>
        </div>
  
        <button @click="editData">EDIT</button>
      </div>
      <!-- <div>
        <button @click="getPost">Get Post</button>
        <div v-for="post in posts" :key='post.post_id'>
          {{ post }}
          hiiiii{{ this.images }}
          <div v-for="img in images" :key="img.id">
            <div v-if="img.id==post.post_id">
              <img :src="require(`../assets/${img.img}`)" alt="">
  
              <img :src="originalImages" alt=""> -->
              <!-- {{img.path}} -->
            <!-- </div> -->
          <!-- </div> -->
        <!-- </div> -->
      <!-- </div> -->
    </div>
  </template>
  
  <script>
  import axios from "axios";
  export default{
      name:'editPost',
      data(){
          return{
              path: "../../../backend/",
              city:'',
              state:'',
              place:'',
              description:'',
              category:'',
              value_for_money:'',
              safety:'',
              overall_exp:'',
              username:'',
              posts:[],
              images:[],
              post_id:null
          }
      },
      methods:{
          handleFileChange(event) {
      this.image= event.target.files;
      console.log('dvhgyu',this.image)
    },
      getImage(url)
      {
        return require(url);
      },
      closePopup() {
        this.$emit("close");
      },
      
  
      async editData() {

        if(confirm("Are you done editing your post?")){

        const formData = new FormData();
        // console.log('image',this.image.length)
        // for(let i=0;i<this.image.length;i++){
        //   formData.append('avatar', this.image[i]);
        // }
        let userid = JSON.parse(localStorage.getItem('user')).id
        formData.append('place', this.place)
        formData.append('city', this.city)
        formData.append('state', this.state)
        formData.append('description', this.description)
        formData.append('category', this.category)
        formData.append('value_for_money', parseInt(this.value_for_money))
        formData.append('safety', parseInt(this.safety))
        formData.append('overall_exp', parseInt(this.overall_exp))
        formData.append('user_id', userid)
        formData.append('post_id', this.post_id)
        //console.log(userid)
        // Assuming "selectedFile" contains the file object
        console.log(formData)
        if (this.username == "Admin") {
            this.$router.push({
              name: 'postDetails'
            })
          } else {
            this.$router.push({
              name: 'myPosts'
            })
          }

        try {
          //   const result = await axios.post('http://localhost:5000/edit', formData, {
          //     headers: {
          //       'Content-Type': 'multipart/form-data'
          //     }
          //   }
          const result = await axios.post('http://localhost:5000/edit', {
            place: this.place,
            city: this.city,
            state: this.state,
            description: this.description,
            category: this.category,
            value_for_money: parseInt(this.value_for_money),
            safety: parseInt(this.safety),
            overall_exp: parseInt(this.overall_exp),
            user_id: userid,
            post_id: this.post_id
          });
          console.log(result);
          
          
          // Handle the response from the server
        } catch (error) {
          console.error(error);
          // Handle the error
        }
        // this.$emit("editData");
      }
      },
  
      },
      mounted(){
      this.username= JSON.parse(localStorage.getItem('user')).name
      console.log('username',this.username)
      },
      created() {
        console.log(JSON.parse(this.$route.params.data))
        this.posts = JSON.parse(this.$route.params.data)
        this.city = this.posts.city
        this.place = this.posts.place
        this.state = this.posts.state_name
        this.description = this.posts.description
        this.value_for_money = this.posts.value_for_money
        this.safety = this.posts.safety
        this.overall_exp = this.posts.overall_exp
        this.category = this.posts.category
        this.post_id = this.posts.post_id
      }
  }
  </script>
  
  
  <style scoped>
  
  /* .parent-div {
    background-image: url('https://i.ibb.co/dmr3yCD/seemless-blackbg.jpg'); 
    background-repeat: repeat;
    background-size: 30%;
    min-height: 100vh; 
    display: flex;
    justify-content: center; 
    align-items: center;
  } */
  
  .popup {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.7);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 1;
  }
  
  .form-container {
    background-color: #161616; 
    padding: 20px; 
    border-radius: 8px; 
    box-shadow: 0 2px 6px rgba(0, 0, 0, 0.5); 
    /* width: 50vw;  */
    /* height: 80vh; */
    padding-bottom: 40px;
  }
  
  .divider {
    height: 2px;
    width: 100%; 
    background-color: rgba(78, 78, 78, 0.7);
    margin-top: 20px;
    /* margin-bottom: 20px;  */
  }
  
  h1{
    text-align: center;
    color: white;
    /* font-family: 'Comfortaa', cursive; */
    font-family: Arial, Helvetica, sans-serif;
    font-size: 24px;
    margin-top: 5px;
  }
  
  .profile {
    display: flex;
    align-items: center;
    margin-bottom: 0px;
    margin-top: 10px;
  }
  
  .profile img {
    width: 60px;
    height: 60px;
    border-radius: 50%;
    margin-right: 10px;
  }
  
  .username-category {
    margin-bottom: 10px;
  }
  
  .username {
    font-family: Arial, Helvetica, sans-serif;
    font-weight: bold;
    font-size: 18px;
    margin-right: 10px;
    margin-bottom: 0;
    color: white;
  }
  
  #category{
    background-color: rgba(108, 105, 105, 0.33);
    color:white;
    border: none;
    border-radius: 5px;
    outline: none;
  }
  
  #category option{
    background-color: black;
  }
  
  /*This part isn' working */
  #category option:hover{  
    background-color: white;  
    color: black;
  }
  
  .input-row {
    display: flex;
    justify-content: space-around;
    margin-bottom: 20px;
  
  }
  
  input[type="text"]{
    background-color: black;
    color: white;
    padding: 10px;
    margin-bottom: 5px;
    border: none;
    outline: none;
    width: 24%;
    font-size: 16px;
    border-radius: 3px;
  }
  
  #value_for_money, #safety, #overall_exp{
    background-color: black;
    color: rgba(250, 250, 250, 0.7);
    padding: 10px;
    margin-bottom: 5px;
    border: none;
    outline: none;
    width: 27%;
    font-size: 16px;
    border-radius: 3px;
    appearance: none; /*Hides the down arrow */
  }
  
  #value_for_money option, #safety option, #overall_exp option{
    color:white;
  }
  
  
  
  .input::placeholder {
    color: rgba(250, 250, 250, 0.7);
    font-size: 16px;
  }
  
  /* Unable to align it to the center */
  textarea {
    width: 97%;
    height: 200px;
    resize: none;
    margin-bottom: 20px;
    padding: 10px;
    background-color: #161616;
    color: white;
    line-height: 1.7rem;
    border: none;
    outline: none;
    font-size: 16px;
  }

  textarea::-webkit-scrollbar {
  width: 10px;
  background-color: transparent;
}

/* (the draggable part) */
textarea::-webkit-scrollbar-thumb {
  background-color: black;
  border-radius: 5px;
}

textarea::-webkit-scrollbar-thumb:hover {
  background-color: #333;
  cursor: pointer; /*not working*/
}
  .upload-btn {
    background-color: transparent;
    border: 1px solid rgba(78, 78, 78, 0.7);
    color: white;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 10px;
    border-radius: 5px;
    font-family: Arial, Helvetica, sans-serif;
    margin-bottom: 20px;
    width:90%;
    margin-left: auto;
    margin-right: auto;
  }
  
  .upload-btn i {
    margin-left: 5px;
  }
  
  /* input #file-upload-button{ */
    /* display:none; */
  /* } */
  
  .rating-row {
    display: flex;
    justify-content: space-around;
    margin-bottom: 20px;
  }
  
  button {
    display: block;
    margin: 0 auto;
    width: 94%;
    background-color: white;
    color: black;
    margin-top: 20px;
    margin-bottom: 20px;
    padding: 2% 20%;
    border: none;
    outline: none;
    font-size: 15px;
    letter-spacing: 0.05cm;
    transition: all 0.3s;
    border-radius: 5px;
  }
  
  button:hover {
    /* background-color: transparent; */
    /* border: 2px solid white !important; */
    /* color: white; */
    transform: scale(1.05);
  }
  
  
  </style>