Hi Jaydeep,

Currently, when we do not set the SD Coefficient in the UI, the API saves it as zero. However, as per the JIRA , we want it to be saved as 1 when the user does not select any value.

I was thinking that instead of setting this default in the backend, it might be better to set it directly in the UI. This way, the user will see 1 as the default value and won't be surprised by the behavior if they leave it unchanged.

Let me know your thoughts.