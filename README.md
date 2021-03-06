# Hashtags

This tool is a rewrite of the Hatnote Hashtags tool.

## Version 2?

The tool should do most if not all of what the [previous tool](https://github.com/hatnote/hashtag-search) did but with some changes and improvements:

Most notably, this version monitors the [recentchanges EventStream](https://wikitech.wikimedia.org/wiki/EventStreams) rather than periodically reading from the recentchanges database. This means all Wikimedia projects and languages are monitored (except Wikidata, see below), and new hashtag uses are ingested into the tool's database almost as soon as they happen.

This version of the tool runs on Django, rather than Flask. No important reason for this, it's just the framework Sam knows best. This does mean that the backend has almost separate code to the old tool.

Bot edits are excluded. One of the reasons the old Hashtags tool slowed to a crawl and had to be taken down was the huge number of bot edits that made their way into the database. While in an ideal world we would collect these too, it's simply too easy to overload the database with millions of entries that very few people are interested in. If individual bot edits need to be tracked, the better solution would be to do so directly via the bot or by looking at the bot account's contributions.

Wikidata is currently excluded too, for similar reasons. A huge number of automated or semi-automated edits happen there utilising hashtag edit summaries, and from initial testing it looked like similar problems were going to present themselves. This will be revisited once the tool is up and running and stable.

This tool is also running on a Horizon VPS instance, rather than Toolforge, to ensure it has the database resources it needs and doesn't disrupt other tools.

## Setup

To set the tool up for local development, you will need:

* [Docker](https://www.docker.com)
* [Docker Compose](https://docs.docker.com/compose/install)

If you are installing Docker on Mac or Windows, Docker Compose is likely already included in your install.

After installing both and cloning this repository, start it by running:

>docker-compose up --build

The `-d` option will allow you to run in detached mode.

When the tool is first run the database takes a little while to start up, but Docker Compose won't natively wait for the database container to finish before starting to run the app. The app will error in multiple places if the db isn't running yet. There are solutions to this that <a href="https://phabricator.wikimedia.org/T207277">will be implemented eventually</a>.

To fix this problem, simply wait for the database to start, then re-build the containers. You will likely need to do this twice because the scripts container will then complain about not being able to find the relevant database table until the app container has finished running migrations.

You should now be able to access the tool on `127.0.0.1`.

Run tests with:

>docker exec -it hashtags_app_1 python manage.py test
