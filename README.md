## Test if findex is suitable for your project:

1. Create an iframe DOM in your local project, put the src='https://iframe.findex.pro'.
2. Add the code below in your project, replace 'example11111' with your eosAccount. please ensure it is executed after the iframe has loaded.
   ```javascript
   const target = document.getElementsByTagName('iframe')[0].contentWindow
   target.postMessage(
       JSON.stringify({
           isSuccessful: true,
           args: {
               name: 'example11111',
               eosAccount: 'example11111',
               type: 1
           },
           msgId: 'parentLogin'
       }),
       '*'
   )
   ```
3. Run your project, if you can see your balance in the trading pannel, congratulation, let's start the integration.

## Integration findex in your project:

1. Add event listener in your project:
   ```javascript
   window.addEventListener('message', (e) => {
         tackleReceiveMessage(e)
       }, 
       false
   );
   ```
2. Customize your tackleReceiveMessage function, here is an example:
   
   ```javascript
    tackleReceiveMessage = (e) => {
         try {
           let message = JSON.parse(e.data)
           if (message.targetFunc === 'execute') {
             //execute() is called when user set or cancel order，there is an example below
             execute(message, e)
           } else if (message.targetFunc === 'setIframeHeight') {
             //setIframeHeight would allow you to set the height of iframe dynamically
             //setIframeHeight(message.iframeHeight)
           }
         } catch (err) {
           console.error('failed to parse message ' + err)
         }
       }
   ```
3. Realise the execute function appeared in step 2.
   
   ```javascript
    const execute = (message, e) => {   
         const data = message.data
         //console.log(data)
         //scatter is which you got after login using scatter
         scatter.eos.transaction(data).then(() => {
           //the format of data is fixed, you are suggested not to change.
           let data = JSON.stringify({ isSuccessful: true, args: {}, msgId: message.msgId })
           e.source.postMessage(data, e.origin)
         }).catch((err) => {
           //the format of data is fixed, you are suggested not to change.
           let data = JSON.stringify({ isSuccessful: false, args: err, msgId: message.msgId })
           e.source.postMessage(data, e.origin)
         })
     }
   ```

4. Let Findex know when user login or logout：
      
   ```javascript
    //Call these two function when user login or logout.
    //Reminder: If execute these two function before iframe has finished loading, Findex would fail in login or logout
    const addAccountToDex = () => { 
          //Be sure select the right Findex iframe  
          const target = document.getElementsByTagName('iframe')[0].contentWindow
          target.postMessage(
              JSON.stringify({
                  isSuccessful: true,
                  args: {
                      name: 'example11111',
                      eosAccount: 'example11111',
                      type: 1
                  },
                  msgId: 'parentLogin'
              }),
              '*'
          )    
     };
       
     const removeAccountFromDex = () => {
          //Be sure select the right Findex iframe
          const target = document.getElementsByTagName('iframe')[0].contentWindow
          target.postMessage(
              JSON.stringify({
                   isSuccessful: true,
                   args: {},
                   msgId: 'parentLogout'
              }),
              '*'
          )
     };
       
     ```
     
 5. Check if findex can login/logout in your project, also set buy/sell/cancel orders. If everything is OK, You are going to succeed. Now contact Findex team to customize 'your findex'.
