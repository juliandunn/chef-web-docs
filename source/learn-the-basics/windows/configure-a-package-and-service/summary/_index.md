## Summary

You saw how to work with the package and service resources. You now know how to work with three types of resources: [file][file], [powershell_script][powershell_script], and [service][service].

You also saw how to apply multiple actions. But how does Chef know what order to apply resources and actions?

### Chef works in the order you specify

Let's take another quick look at our web server recipe.

```ruby-Win32
# ~\chef-repo\webserver.rb
powershell_script 'Install IIS' do
  code 'Add-WindowsFeature Web-Server'
  guard_interpreter :powershell_script
  not_if "(Get-WindowsFeature -Name Web-Server).Installed"
end

service 'w3svc' do
  action [:enable, :start]
end

file 'c:\inetpub\wwwroot\Default.htm' do
  content '<html>
  <body>
    <h1>hello world</h1>
  </body>
</html>'
end
```

The resources are applied in the order they are specified in the recipe. So here IIS is installed, then the W3SVC service is configured, and finally the home page is set. If any resource is already in the desired state, Chef simply moves on to the next one.

The same idea applies to the action list `[:enable, :start]` for configuring the W3SVC service. The service is enabled when the server boots before the service is started.

It's important to always think about how you order things. For example, the recipe wouldn't work if we tried to configure the W3SVC service before IIS is even installed.

A recipe stops if any step fails (don't worry &ndash; Chef provides info about the error). That's why we ordered the service actions the way we did. If the service can't be enabled on boot then we don't want to start it.

[file]: https://docs.chef.io/resource_file.html
[powershell_script]: https://docs.chef.io/resource_powershell_script.html
[service]: https://docs.chef.io/resource_service.html