


I'm working on a project where I've included a Pro Starter copy of Datomic.


The clojure (leiningen) way to do it . No gpg or other parts.

Using environment variables for the secrets, so you can deploy this in your local machine, a vagrant box, heroku or wherever quickly and safely without adding secrets to github.

BTw check environ for that.

First thing to note: your password is not your site (https://my.datomic.com) password - you need to get inside to get an automatically generated secret on behalf of datomic. Then:

Add the following to your project.clj:
:repositories [["my.datomic.com" {:url "https://my.datomic.com/repo"
                                  :username :env/mydatomiccomusername
                                  :password :env/mydatomiccompassword}]]

:dependencies   [                 [com.datomic/datomic-pro "0.9.5385"]
                                  [environ "1.0.3"]]
(of course add the datomic dependeny to your existing list if you have it)

Et voila you are finished.

Well actually you are finished in your project but you also need to

export MYDATOMICCOMUSERNAME="(your email)"
export MYDATOMICCOMPASSWORD="(look for this inside the website)"
echo "This is a quick test that" $MYDATOMICCOMUSERNAME "and" $MYDATOMICCOMPASSWORD "have been properly stored!"

A dumb note: I just did VAR=val without export and quotes and didnt work, dont forget those! actually probably only one caused an error, todo : chekc

Also comment a little bit on managing secrets and database connections

A good comment on the deply + repository inof of leiningen: use gpg or encrypt your whole disk >)
