# How to bypass reCAPTCHA 
<p>No matter how many times people wrote that the captcha has outlived itself long time ago and no longer works as effectively as its developers would have liked initially, however, the owners of Internet resources continue to protect their projects with captchas. But what is the most popular captcha of our time?</p>
<p>Clarification – all the code that will be presented in this article has been prepared based on the API documentation of the captcha recognition service 2captcha</p>
<p>It is reCAPTCHA. ReCAPTCHA V2, V3, etc., that was created by Google back in 2007. It has been many years since the first reCAPTCHA appeared, but it continues to keep the garland, periodically losing ground to competitors and then winning it back. But reCAPTCHA has never taken the 2nd place in popularity, despite all its imperfections in front of neural networks.</p>
<p>There was a huge number of attempts to create a "reCAPTCHA killer", some were less successful, some only looked like a threat to reCAPTCHA, but in fact turned out to be nothing. Yet the fact remains that the desire of competitors to do something better and more reliable than reCAPTCHA demonstrates its popularity.</p>
<h2>How to bypass reCAPTCHA using python (example of a code)</h2>
<p>If you do not trust any third-party modules, I have prepared the most universal code that can be inserted into your Python script with minor modifications and solve the reCAPTCHA automatically. Here is the code itself:</p>
```python
  import requests
import time

API_KEY = 'Your_API_2Captcha_key'

def solve_recaptcha_v2(site_key, url):
    payload = {
        'key': API_KEY,
        'method': 'userrecaptcha',
        'googlekey': site_key,
        'pageurl': url,
        'json': 1
    }
    
    response = requests.post('https://2captcha.com/in.php', data=payload)
    result = response.json()

    if result['status'] != 1:
        raise Exception(f"Error when sending captcha: {result['request']}")

    captcha_id = result['request']
    
    while True:
        time.sleep(5)
        response = requests.get(f"https://2captcha.com/res.php?key={API_KEY}&action=get&id={captcha_id}&json=1")
        result = response.json()

        if result['status'] == 1:
            print("Captcha solved successfully.")
            return result['request']
        elif result['request'] == 'CAPCHA_NOT_READY':
            print("The captcha has not been solved yet, waiting...")
            continue
        else:
            raise Exception(f"Error while solving captcha: {result['request']}")

def solve_recaptcha_v3(site_key, url, action='verify', min_score=0.3):
    payload = {
        'key': API_KEY,
        'method': 'userrecaptcha',
        'googlekey': site_key,
        'pageurl': url,
        'version': 'v3',
        'action': action,
        'min_score': min_score,
        'json': 1
    }

    response = requests.post('https://2captcha.com/in.php', data=payload)
    result = response.json()

    if result['status'] != 1:
        raise Exception(f"Error when sending captcha: {result['request']}")

    captcha_id = result['request']
    
    while True:
        time.sleep(5)
        response = requests.get(f"https://2captcha.com/res.php?key={API_KEY}&action=get&id={captcha_id}&json=1")
        result = response.json()

        if result['status'] == 1:
            print("Captcha solved successfully.")
            return result['request']
        elif result['request'] == 'CAPCHA_NOT_READY':
            print("The captcha has not been solved yet, waiting...")
            continue
        else:
            raise Exception(f"Error while solving captcha: {result['request']}")

# Usage example for reCAPTCHA v2
site_key_v2 = 'your_site_key_v2'
url_v2 = 'https://example.com'
recaptcha_token_v2 = solve_recaptcha_v2(site_key_v2, url_v2)
print(f"Received token for reCAPTCHA v2: {recaptcha_token_v2}")

# Usage example for reCAPTCHA v3
site_key_v3 = 'your_site_key_v3'
url_v3 = 'https://example.com'
recaptcha_token_v3 = solve_recaptcha_v3(site_key_v3, url_v3)
print(f"Received token for reCAPTCHA v3: {recaptcha_token_v3}")
```
<p>However, before using the provided script, carefully read the recommendations of the service for recognizing a particular type of reCAPTCHA in order to have an idea how this code works.</p>
<p></p>Also, do not forget to insert your API key in the code and, of course, install the necessary modules.</p>

<h2>How to bypass reCAPTCHA in node js</h2>
<p>As in the case of Python, for those who do not like ready-made solutions, below is a script for solving a captcha using the node js programming language. I remind you to not forget to install the necessary modules for the code to work, including:</p>
<code>axios</code>
<p>You can install it using this command –</p>
<code>
npm install axios
</code>
<p>Here is the code itself:</p>
<pre>const axios = require('axios');
const sleep = require('util').promisify(setTimeout);

const API_KEY = 'YOUR_API_KEY_2CAPTCHA'; // Replace with your real API key

// Function for reCAPTCHA v2 solution
async function solveReCaptchaV2(siteKey, pageUrl) {
    try {
        // Sending a request for the captcha solution
        const sendCaptchaResponse = await axios.post(`http://2captcha.com/in.php`, null, {
            params: {
                key: API_KEY,
                method: 'userrecaptcha',
                googlekey: siteKey,
                pageurl: pageUrl,
                json: 1
            }
        });

        if (sendCaptchaResponse.data.status !== 1) {
            throw new Error(`Error when sending captcha: ${sendCaptchaResponse.data.request}`);
        }

        const requestId = sendCaptchaResponse.data.request;
        console.log(`Captcha sent, request ID: ${RequestId}`);

        // Waiting for the captcha solution
        while (true) {
            await sleep(5000); // Waiting 5 seconds before the next request

            const getResultResponse = await axios.get(`http://2captcha.com/res.php`, {
                params: {
                    key: API_KEY,
                    action: 'get',
                    id: requestId,
                    json: 1
                }
            });

            if (getResultResponse.data.status === 1) {
                console.log('Captcha solved successfully.');
                return getResultResponse.data.request;
            } else if (getResultResponse.data.request === 'CAPCHA_NOT_READY') {
                console.log('The captcha has not been solved yet, waiting...');
            } else {
                throw new Error(`Error while solving captcha: ${getResultResponse.data.request}`);
            }
        }
    } catch (error) {
        console.error(`An error occurred: ${error.message}`);
    }
}

// Function for reCAPTCHA v3 solution
async function solveReCaptchaV3(siteKey, pageUrl, action = 'verify', minScore = 0.3) {
    try {
        // Sending a request for the captcha solution
        const sendCaptchaResponse = await axios.post(`http://2captcha.com/in.php`, null, {
            params: {
                key: API_KEY,
                method: 'userrecaptcha',
                googlekey: siteKey,
                pageurl: pageUrl,
                version: 'v3',
                action: action,
                min_score: minScore,
                json: 1
            }
        });

        if (sendCaptchaResponse.data.status !== 1) {
            throw new Error(`Error when sending captcha: ${sendCaptchaResponse.data.request}`);
        }

        const requestId = sendCaptchaResponse.data.request;
        console.log(`Captcha sent, request ID: ${RequestId}`);

        // Waiting for the captcha solution
        while (true) {
            await sleep(5000); // Waiting 5 seconds before the next request

            const getResultResponse = await axios.get(`http://2captcha.com/res.php`, {
                params: {
                    key: API_KEY,
                    action: 'get',
                    id: requestId,
                    json: 1
                }
            });

            if (getResultResponse.data.status === 1) {
                console.log('Captcha solved successfully.');
                return getResultResponse.data.request;
            } else if (getResultResponse.data.request === 'CAPCHA_NOT_READY') {
                console.log('The captcha has not been solved yet, waiting...');
            } else {
                throw new Error(`Error while solving captcha: ${getResultResponse.data.request}`);
            }
        }
    } catch (error) {
        console.error(`An error occurred: ${error.message}`);
    }
}

// Usage example for reCAPTCHA v2
(async () => {
    const siteKeyV2 = 'YOUR_SITE_KEY_V2'; // Replace with the real site key
    const pageUrlV2 = 'https://example.com '; // Replace with the real URL of the page

    const tokenV2 = await solveReCaptchaV2(siteKeyV2, pageUrlV2);
    console.log(`Received token for reCAPTCHA v2: ${tokenV2}`);
})();

// Usage example for reCAPTCHA v3
(async () => {
    const siteKeyV3 = 'YOUR_SITE_KEY_V3'; // Replace with the real site key
    const pageUrlV3 = 'https://example.com '; // Replace with the real URL of the page
    const action = 'homepage'; // Replace with the corresponding action
    const MinScore = 0.5; // Set the minimum allowed score

    const tokenV3 = await solveReCaptchaV3(siteKeyV3, pageUrlV3, action, minScore);
    console.log(`Received token for reCAPTCHA v3: ${tokenV3}`);
})();
</pre>
<p>Also, do not forget to insert your API key into the code, instead of <code>"'YOUR_API_KEY_2CAPTCHA'"</code></p>
<h2>How to recognize a reCAPTCHA in PHP</h2>
<p>Well, for those who are not used to using ready-made modules, here is the code for integration directly. The code uses standard PHP functions such as file_get_contents and json_decode, here is the code itself:</p>
<pre><?php

function solveRecaptchaV2($apiKey, $siteKey, $url) {
    $requestUrl = "http://2captcha.com/in.php?key={$apiKey}&method=userrecaptcha&googlekey={$siteKey}&pageurl={$url}&json=1";
    
    $response = file_get_contents($requestUrl);
    $result = json_decode($response, true);

    if ($result['status'] != 1) {
        throw new Exception("Error when sending captcha: " . $result['request']);
    }

    $captchaId = $result['request'];

    while (true) {
        sleep(5);
        $resultUrl = "http://2captcha.com/res.php?key={$apiKey}&action=get&id={$captchaId}&json=1";
        $response = file_get_contents($resultUrl);
        $result = json_decode($response, true);

        if ($result['status'] == 1) {
            return $result['request'];
        } elseif ($result['request'] == 'CAPCHA_NOT_READY') {
            continue;
        } else {
            throw new Exception("Error while solving captcha: " . $result['request']);
        }
    }
}

function solveRecaptchaV3($apiKey, $siteKey, $url, $action = 'verify', $minScore = 0.3) {
    $requestUrl = "http://2captcha.com/in.php?key={$apiKey}&method=userrecaptcha&googlekey={$siteKey}&pageurl={$url}&version=v3&action={$action}&min_score={$minScore}&json=1";
    
    $response = file_get_contents($requestUrl);
    $result = json_decode($response, true);

    if ($result['status'] != 1) {
        throw new Exception("Error when sending captcha: " . $result['request']);
    }

    $captchaId = $result['request'];

    while (true) {
        sleep(5);
        $resultUrl = "http://2captcha.com/res.php?key={$apiKey}&action=get&id={$captchaId}&json=1";
        $response = file_get_contents($resultUrl);
        $result = json_decode($response, true);

        if ($result['status'] == 1) {
            return $result['request'];
        } elseif ($result['request'] == 'CAPCHA_NOT_READY') {
            continue;
        } else {
            throw new Exception("Error while solving captcha: " . $result['request']);
        }
    }
}

// Usage example for reCAPTCHA v2
$apiKey = 'YOUR_API_KEY_2CAPTCHA';
$siteKeyV2 = 'YOUR_SITE_KEY_V2';
$urlV2 = 'https://example.com';

try {
    $tokenV2 = solveRecaptchaV2($apiKey, $siteKeyV2, $urlV2);
    echo "Received token for reCAPTCHA v2: {$tokenV2}\n";
} catch (Exception $e) {
    echo "Error: " . $e->getMessage() . "\n";
}

// Usage example for reCAPTCHA v3
$siteKeyV3 = 'YOUR_SITE_KEY_V3';
$urlV3 = 'https://example.com';
$action = 'homepage'; // Specify the appropriate action
$MinScore = 0.5; // Specify the minimum allowed score

try {
    $tokenV3 = solveRecaptchaV3($apiKey, $siteKeyV3, $urlV3, $action, $minScore);
    echo "Received token for reCAPTCHA v3: {$tokenV3}\n";
} catch (Exception $e) {
    echo "Error: " . $e->getMessage() . "\n";
}

?>

I also remind you of the need to replace some parameters in the code, in particular:
$apiKey = 'YOUR_API_KEY_2CAPTCHA';
 $siteKeyV2 = 'YOUR_SITE_KEY_V2';
$urlV2 = 'https://example.com';
</pre>
<p>Thus, using the examples given, you can solve most of the issues related to reCAPTCHA recognition. You can ask questions in the comments if there are any left!</p>

