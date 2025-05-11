<template>
    <div>
        <h1>Accuracy test Sentiment</h1>
        <pre>{{ }}</pre>
    </div>
    <button @click="change_data('tcas')" style="margin: 5px;">Change Data : Tcas61</button>
    <button @click="change_data('general-amy')" style="margin: 5px;">Change Data : General</button>
    <button @click="change_data('review_shopping')" style="margin: 5px;">Change Data : Review_shopping</button>
    <div class="container">
        <p> Accuracy : {{ acc }} % , quantity : {{ quantity }}  , correct : {{ correct }} , incorrect : {{ incorrect }} , neutral : {{  neutral }}</p>
        <table>
            <thead>
                <tr>
                    <th style="border-radius: 10px 0px 0px 0px;">No.</th>
                    <th>Message</th>
                    <th>Actual value</th>
                    <th>Sentiment</th>
                    <th style="border-radius: 0px 10px 0px 0px;">correctness</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(item, index) in test_json" :key="item">
                    <td>{{ index + 1 }}</td>
                    <td class="name">{{ item.text }}</td>
                    <td>
                        <p v-if="item.real_sentiment == 'positive'"> 
                            <!-- {{ item.real_sentiment }} -->
                            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" style="fill: #16a34a; --darkreader-inline-fill: #e8e6e3;" data-darkreader-inline-fill=""><path d="M12 2C6.486 2 2 6.486 2 12s4.486 10 10 10 10-4.486 10-10S17.514 2 12 2zm3.493 6a1.494 1.494 0 1 1-.001 2.987A1.494 1.494 0 0 1 15.493 8zM8.5 8a1.5 1.5 0 1 1-.001 3.001A1.5 1.5 0 0 1 8.5 8zM12 18c-5 0-6-5-6-5h12s-1 5-6 5z"></path></svg>
                        </p>
                        <p v-else-if="item.real_sentiment == 'negative'"> 
                            <!-- {{ item.real_sentiment }} -->
                            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" style="fill: #f87171; --darkreader-inline-fill: #e8e6e3;" data-darkreader-inline-fill=""><path d="M12 2C6.486 2 2 6.486 2 12s4.486 10 10 10 10-4.486 10-10S17.514 2 12 2zm-5 8.5a1.5 1.5 0 1 1 3.001.001A1.5 1.5 0 0 1 7 10.5zM8 17s1-3 4-3 4 3 4 3H8zm7.493-5.014a1.494 1.494 0 1 1 .001-2.987 1.494 1.494 0 0 1-.001 2.987z"></path></svg>
                        </p>
                        <p v-else-if="item.real_sentiment == 'neutral'">
                             <!-- {{ item.real_sentiment }} -->
                            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" style="fill: rgb(255, 255, 255); --darkreader-inline-fill: #e8e6e3;" data-darkreader-inline-fill=""><path d="M12 2C6.486 2 2 6.486 2 12s4.486 10 10 10 10-4.486 10-10S17.514 2 12 2zm-5 8.5a1.5 1.5 0 1 1 3.001.001A1.5 1.5 0 0 1 7 10.5zm9 6.5H7.974v-2H16v2zm-.507-5.014a1.494 1.494 0 1 1 .001-2.987 1.494 1.494 0 0 1-.001 2.987z"></path></svg>
                        </p>
                    </td>
                    <td>
                        <p v-if="item.sentiment == 'positive'"> 
                            <!-- {{ item.sentiment }} -->
                            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" style="fill: #16a34a; --darkreader-inline-fill: #e8e6e3;" data-darkreader-inline-fill=""><path d="M12 2C6.486 2 2 6.486 2 12s4.486 10 10 10 10-4.486 10-10S17.514 2 12 2zm3.493 6a1.494 1.494 0 1 1-.001 2.987A1.494 1.494 0 0 1 15.493 8zM8.5 8a1.5 1.5 0 1 1-.001 3.001A1.5 1.5 0 0 1 8.5 8zM12 18c-5 0-6-5-6-5h12s-1 5-6 5z"></path></svg>
                        </p>
                        <p v-else-if="item.sentiment == 'negative'"> 
                            <!-- {{ item.sentiment }} -->
                            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" style="fill: #f87171; --darkreader-inline-fill: #e8e6e3;" data-darkreader-inline-fill=""><path d="M12 2C6.486 2 2 6.486 2 12s4.486 10 10 10 10-4.486 10-10S17.514 2 12 2zm-5 8.5a1.5 1.5 0 1 1 3.001.001A1.5 1.5 0 0 1 7 10.5zM8 17s1-3 4-3 4 3 4 3H8zm7.493-5.014a1.494 1.494 0 1 1 .001-2.987 1.494 1.494 0 0 1-.001 2.987z"></path></svg>
                        </p>
                        <p v-else-if="item.sentiment == 'neutral'">
                             <!-- {{ item.sentiment }} -->
                            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" style="fill: rgb(255, 255, 255); --darkreader-inline-fill: #e8e6e3;" data-darkreader-inline-fill=""><path d="M12 2C6.486 2 2 6.486 2 12s4.486 10 10 10 10-4.486 10-10S17.514 2 12 2zm-5 8.5a1.5 1.5 0 1 1 3.001.001A1.5 1.5 0 0 1 7 10.5zm9 6.5H7.974v-2H16v2zm-.507-5.014a1.494 1.494 0 1 1 .001-2.987 1.494 1.494 0 0 1-.001 2.987z"></path></svg>
                        </p>
                    </td>
                    <td>
                        <p v-if="item.true_false == true">
                            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24"
                                style="fill: #16a34a; --darkreader-inline-fill: #e8e6e3;" data-darkreader-inline-fill="">
                                <path d="m10 15.586-3.293-3.293-1.414 1.414L10 18.414l9.707-9.707-1.414-1.414z"></path>
                            </svg>
                        </p>
                        <p v-else-if="item.true_false == false">
                            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24"
                                style="fill: #f87171; --darkreader-inline-fill: #e8e6e3;" data-darkreader-inline-fill="">
                                <path
                                    d="m16.192 6.344-4.243 4.242-4.242-4.242-1.414 1.414L10.535 12l-4.242 4.242 1.414 1.414 4.242-4.242 4.243 4.242 1.414-1.414L13.364 12l4.242-4.242z">
                                </path>
                            </svg>
                        </p>
                    </td>
                </tr>
                <tr>
                    <td style="border-radius: 0px 0px 0px 10px;"></td>
                    <td></td>
                    <td></td>
                    <td></td>
                    <td style="border-radius: 0px 0px 10px 0px;"></td>
                </tr>
            </tbody>
        </table>
    </div>
    <p>https://github.com/PyThaiNLP/thai-sentiment-analysis-dataset</p>
</template>


<script>
import { ref, onMounted } from "vue";
import axios from 'axios';

export default {
    setup() {
        const csvData = ref(null);
        const quantity = ref(0);
        const acc = ref(0);
        const correct = ref(0);
        const incorrect = ref(0);
        const neutral = ref(0);
        const test_json = ref();
        const test = async (index) => {
            try {
                quantity.value = 0;
                acc.value = 0;
                correct.value = 0;
                incorrect.value = 0;
                neutral.value =0;
                const response = await axios.get(`/src/json/${index}.json`);

                test_json.value = await Promise.all(response.data.map(async (data) => {
                    var polarity = await data.text.slice(-3);
                    var result = await sentiment(data.text.slice(0, -3));
                    var true_false = true;

                    console.log(data.text);
                    console.log("polarity : ", polarity);
                    console.log("result : ", result.slice(0, 3));
                    if (result.slice(0, 3) == polarity) {
                        correct.value += 1;
                        true_false = true
                    } else if (result == "") {
                        incorrect.value += 1;
                        neutral.value += 1;
                        result = "neutral"
                        true_false = false
                    } else {
                        incorrect.value += 1;
                        true_false = false
                    }
                    if (polarity == "pos") {
                        polarity = "positive"
                    } else {
                        polarity = "negative"
                    }
                    quantity.value += 1;
                    acc.value =  ((correct.value / quantity.value) * 100 ).toFixed(2)
                    return {
                        text: data.text.slice(0, -3),
                        real_sentiment: polarity,
                        sentiment: result,
                        true_false: true_false
                    }
                }));
                console.log("quantity : ", quantity.value);
                console.log("correct : ", correct.value);
                console.log("incorrect : ", incorrect.value);
                console.log(" Acc : ",acc.value);
                console.log(test_json.value);
            } catch (error) {
                console.error(error);
            }
        };
        const sentiment = async (reviews) => {
            try {
                const response = await axios.get("https://api.aiforthai.in.th/ssense", {
                    headers: {
                        "Content-Type": "application/x-www-form-urlencoded",
                        Apikey: "nn6tZctdSvv8RASCk8oU62F0QWTbSB7s"
                    },
                    params: {
                        text: reviews
                    }
                });
                // console.log(response.data);
                const polarity = response.data.sentiment.polarity;

                return polarity

            } catch (error) {
                quantity.value -= 1;
                console.error(error);
                return "";
            }
        };
        const change_data = (index) => {
            test(index)
        }
        onMounted(async () => {
            await test("tcas");
        });

        return {
            csvData,
            quantity,
            correct,
            incorrect,
            test_json,
            acc,
            neutral,
            change_data
        };
    },
};
</script>



<style scoped>
.read-the-docs {
    color: #888;
}

html,

table {
    width: 1280px;
    border-collapse: collapse;
    overflow: hidden;
    box-shadow: 0 0 20px rgba(0, 0, 0, 0.1);
}

th,
td {
    padding: 15px;
    background-color: rgba(255, 255, 255, 0.021);
    color: #fff;
    text-transform: capitalize;
}

td.name {
    max-width: 330px;
    overflow-wrap: break-word;
}

th {
    text-align: center;
}

thead {
    th {
        background-color: #000000;
    }
}

tbody {
    tr {
        &:hover {
            background-color: rgba(255, 255, 255, 0.2);
        }
    }
}</style>
