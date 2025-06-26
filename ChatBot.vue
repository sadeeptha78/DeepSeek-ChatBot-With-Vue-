<template>
    <div class="container">
        <h2>Free ChatBot</h2>
        <div class="form-group">
            <input
                type="text"
                class="form-control"
                v-model="userInput"
                placeholder="Enter your question" />
        </div>
        <button class="btn btn-success" @click="sendMessage">Ask!</button>

        <div v-html="response" class="mt-3"></div>
    </div>
</template>

<script>
import { ref } from 'vue';
import { marked } from 'marked';


export default {
    name: "ChatBot",

    setup() {
        const userInput = ref('');
        const response = ref('');

        async function sendMessage() {
            if (!userInput.value) {
                response.value = 'Please enter a message.';
                return;
            }

            response.value = 'Loading...';

            try {
                const res = await fetch(
                    'https://openrouter.ai/api/v1/chat/completions',
                    {
                        method: 'POST',
                        headers: {
                            Authorization: 'Bearer <API_KEY>',
                            'HTTP-Referer': 'https://www.sitename.com',
                            'X-Title': 'SiteName',
                            'Content-Type': 'application/json',
                        },
                        body: JSON.stringify({
                            model: 'deepseek/deepseek-r1:free',
                            messages: [{ role: 'user', content: userInput.value }],
                        }),
                    }
                );

                const data = await res.json();
                const markdownText = data.choices?.[0]?.message?.content || 'No response received.';

                response.value = marked(markdownText);

            } catch (error) {
                response.value = 'Error: ' + error.message;
            }
        }

        return {
            userInput,
            response,
            sendMessage
        };
    }
};
</script>

<style scoped>
.mt-3 {
    margin-top: 1rem;
}
</style>
