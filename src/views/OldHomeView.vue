<script setup>
import { ref, watch } from 'vue';
import { useToast } from 'vue-toast-notification';
import 'vue-toast-notification/dist/theme-sugar.css';
import { onMounted } from 'vue';

const API_KEY = "Your secret key here";

const $toast = useToast();
const selected = ref(false);
const isVisible = ref(false);
const prompt = ref("");

const typedText = ref('');
const fullText = 'Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry\'s standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries, but also the leap into electronic typesetting, remaining essentially unchanged. It was popularised in the 1960s with the release of Letraset sheets containing Lorem Ipsum passages, and more recently with desktop publishing software like Aldus PageMaker including versions of Lorem Ipsum.';


const startTyping = () => {
    typedText.value = '';
    let index = 0;
    const interval = setInterval(() => {
        if (index < fullText.length) {
            typedText.value += fullText.charAt(index);
            index++;
        } else {
            clearInterval(interval);
        }
    }, 50); // Adjust typing speed here
};


const submitData = async () => {
    try {
        const response = await fetch("https://api.openai.com/v1/chat/completions", {
            method: "POST",
            headers: {
                Authorization: `Bearer ${API_KEY}`,
                "Content-Type": "application/json",
            },
            body: JSON.stringify({
                model: "gpt-3.5-turbo-16k",
                prompt: prompt.value,
                max_tokens: 7,
            }),
        });

        if (!response.ok) {
            // Handle HTTP errors
            const errorData = await response.json();
            isVisible.value = true;
            if (isVisible.value) {
                startTyping();
            } else {
                typedText.value = '';
            }
            if (errorData.error && errorData.error.code === "insufficient_quota") {
                $toast.open({
                    message: 'I have run out of credits. Try again later',
                    type: 'error',
                    style: {
                        zIndex: 9999,
                        fontFamily: 'poppins',
                        background: 'black',
                        color: 'white',
                    },
                });
            } else {

                console.error("Error from API:", errorData);
            }
        } else {
            const data = await response.json();
            console.log("Response from API:", data);
        }
    } catch (error) {
        console.error("Error fetching data:", error);
    }
};

const getresponse = () => {
    console.log("Prompt:", prompt.value);
    submitData();
};

const selectPrompt = (text) => {
    prompt.value = text;
    console.log(text)
};

</script>

<template>
    <div class="body">
        <div class="sidebar">
            <div class="logo">
                <h2>Prompt AI</h2>
            </div>
            <div class="sidebarlinks">
                <a href="#">Home</a>
                <a href="#">History</a>
                <a href="#">Notifications</a>
                <a href="#">Settings</a>
            </div>
        </div>
        <div class="main-content">
            <br>
            <h2>Generative AI SASS</h2>
            <p>Welcome Alex, Select a prompt and get your results in seconds...</p>
            <div :class="selected ? 'prompt-buttons button' : 'showselect button'">
                <button
                    @click="selectPrompt('Translate this sentence from English to French: The weather is beautiful today')">Translate
                    this sentence from English to French: 'The weather is beautiful today'.</button>

                <button
                    @click="selectPrompt('Translate this sentence from English to French: The weather is beautiful today')">Translate
                    this sentence from English to French: 'The weather is beautiful today'.
                </button>

            </div>
            <div class="btndiv">
                <button id="send-button" @click="getresponse">Send</button>
            </div>
            <div id="additional-prompts" class="prompt-buttons" v-if="isVisible">
                <button>{{ typedText }}</button>
            </div>
        </div>
    </div>
</template>

<style>
.body {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: Arial, sans-serif;
    display: flex;
    height: 100vh;
    background: #fff;
    color: white;
    font-family: "IBM Plex Sans Thai", sans-serif;
    font-style: normal;
}

.sidebar {
    width: 20%;
    background-color: #2B2155;
    color: #fff;
    padding: 20px;

}

.logo {
    margin-top: 70px;
    Font-weight: 900;
    Font-family: poppins;
    font-size: 22px;
    color: #E2E0E0;
    letter-spacing: -2px;
    display: flex;
    justify-content: center;
}

.sidebar a {
    color: white;
    line-height: 1.4em;
    text-decoration: none;
    margin: 10px 0;
    letter-spacing: 1px;
    font-size: 17px;
    padding: 5px 20px 5px 25px;
    font-weight: 300;
}

.sidebar a:hover {
    line-height: 1.4em;
    text-decoration: none;
    margin: 10px 0;
    letter-spacing: 1px;
    font-size: 17px;
    padding: 5px 20px 5px 25px;
    font-weight: 300;
    border-radius: 7px;
    background-color: white;
    cursor: pointer;
    color: black;
}

.main-content {
    width: 80%;
    padding: 20px;
    text-align: center;
}

.main-content h2 {
    padding-top: 30px;
    font-size: 36px;
    text-align: center;
    font-family: poppins;
    margin: auto;
    font-weight: 600;
    color: black;
}

.main-content p {
    font-size: 17px;
    color: rgb(53, 53, 53);
    margin-top: 2px;
    font-weight: 300;
    margin-bottom: 30px;
}

.prompt-buttons {
    display: flex;
    flex-direction: column;
    gap: 10px;
}

.prompt-buttons button {
    padding: 15px;
    font-size: 18px;
    background: red;
    border: 1px solid rgba(83, 91, 234, 0.2);
    border-radius: 5px;
    cursor: pointer;
    padding-left: 30px;
    width: 86%;
    margin: auto;
    font-size: 15px;
    font-weight: 400;
    margin-top: 10px;
    text-align: left;
}

.showselect {
    display: flex;
    flex-direction: column;
    gap: 10px;
}

.showselect button {
    padding: 15px;
    font-size: 18px;
    background: #F2F2FE;
    border: 1px solid rgba(83, 91, 234, 0.2);
    border-radius: 5px;
    cursor: pointer;
    padding-left: 30px;
    width: 86%;
    margin: auto;
    font-size: 15px;
    font-weight: 400;
    margin-top: 10px;
    text-align: left;
}

.response {
    display: flex;
    flex-direction: column;
    gap: 10px;
}

.response button {
    padding: 15px;
    font-size: 18px;
    background: #F2F2FE;
    border: 1px solid rgba(83, 91, 234, 0.2);
    border-radius: 5px;
    cursor: pointer;
    padding-left: 30px;
    width: 86%;
    margin: auto;
    font-size: 16px;
    line-height: 1.5em;
    font-weight: 400;
    margin-top: 10px;
    text-align: left;
}

.prompt-buttons button:hover {
    background-color: #E0E0FF;
}

#additional-prompts {
    flex-direction: column;
    gap: 10px;
}

.btndiv {
    width: 93%;
}

#send-button {
    display: flex;
    gap: 10px;
    padding: 10px 20px;
    font-size: 15px;
    border: 1px solid rgba(83, 91, 234, 0.2);
    border-radius: 8px;
    cursor: pointer;
    color: white;
    margin-top: 20px;
    margin-bottom: 50px;
    margin-left: auto;
    background-color: #2B2155;
    font-weight: 500;
}


.sidebarlinks {
    margin-left: 20px;
    list-style-type: none;
    padding: 0;
    margin-top: 15px;
    display: flex;
    flex-direction: column;
    margin-bottom: 15px;
    padding: 20px 20px 5px 10px;
    border-radius: 7px;
}
</style>