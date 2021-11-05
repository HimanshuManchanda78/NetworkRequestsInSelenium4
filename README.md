            #region Open chrome/edge browser in private/ingognito
            ChromeOptions options = new ChromeOptions();
            options.AddArguments("--incognito");
            options.AddArgument("no-sandbox");
            options.AddUserProfilePreference("disable-popup-blocking", "true");
            #endregion

            #region Open browser on local machine or on selenium grid
            IWebDriver driver = new RemoteWebDriver(new Uri("enter your selenium grid url here"), options);
            //Example - http://<grid machine ip>:<port>/wd/hub
            driver.Manage().Window.Maximize();
            #endregion

            #region capture network request and inject your SSO Enabled application url and application credentials in it
            INetwork networkInterceptor = driver.Manage().Network;
            var handler = new NetworkAuthenticationHandler()
            {
                UriMatcher = (url) => url.Host == "fake.com" && url.PathAndQuery.Contains("basic_auth"),
                Credentials = new PasswordCredentials(@"mydomain\myuserid001", "mysecretPassword001")
            };
            networkInterceptor.AddAuthenticationHandler(handler);
            networkInterceptor.StartMonitoring();
            #endregion

            #region Login to application and perform some operation on landing page to ensure it is working.
            driver.Navigate().GoToUrl("your application url"+"/basic_auth");
            driver.FindElement(By.XPath("")).Click();           
            #endregion
