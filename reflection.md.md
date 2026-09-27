# Reflection

One design choice I made was having the loyalty discount calculated by real code instead of letting the AI guess the numbers. I built a small Python script that runs inside AWS's Code Interpreter tool, so the discount, points used, and final price are always exact. If that tool fails, a backup calculation still gives the customer a usable answer instead of an error.

Early on, I also had trouble pasting long commands into the terminal. Since some setup commands were quite long, I started writing them into small `.sh` script files and running those instead with a short `bash filename.sh` command. It saved a lot of typing for the rest of the project.

The biggest challenge was the AgentCore Gateway, which lets the agent talk to the order-tracking and refund tools. The agent kept failing with a confusing error. After digging through the AWS logs, I found the real problem — the Gateway required login credentials, but my agent never sent any. The fix was deleting that Gateway and building a new one with "No authorisation" selected, then reconnecting both tools to it.

Along the way, I also hit permission errors more than once — AWS kept blocking the agent from reaching Memory and the refund Lambda function, even though everything looked correctly connected. I had to go into IAM afterward and manually add the missing permissions. It taught me that the real reason behind an error is often hidden a few layers down — you have to keep digging instead of guessing.

If I were building this for a real company, I would tighten the permissions a lot more. Right now I gave the agent fairly wide access to AWS's memory and Gateway services just to get things working. In a real setting, I'd only give it access to the exact actions it needs. I'd also add better error handling, so if an AWS service is slow or down, the agent retries instead of just failing. And I'd set up alerts so a real person finds out quickly when something breaks.
