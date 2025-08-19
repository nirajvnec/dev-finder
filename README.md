Subject: Clarification on credentials for internal container registry

Hi [Architect’s Name],

I have just started working on the activity of creating a container image in my .NET project. For this, I need to pull the base SQL Server image, and I would like it to come from our internal container registry (xyz.net) instead of Docker Hub.

When I run docker login xyz.net, it prompts me for a username and password. At this stage, I am not sure what credentials should be used:

Should I use my AD credentials, or is there a separate account required for the internal container registry?

If a different registration or token setup is required, could you please guide me on the process to get access?


Right now, since I don’t know the correct way to authenticate, Docker defaults to pulling from Docker Hub, which is not what I want. I just need clarity on how to properly authenticate against the internal container registry so I can proceed with the build.

Looking for your guidance on this.
