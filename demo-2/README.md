# Demo 2

## Create Our Application in OpenShift

Database Provider Account needs to be created prior.

1. Under the Administrator persona expand `Home` on the left hand side and click `Projects`.

2. On the right click the button marked `Create Project`.

3. Give your Project a name and display name. Click `Create`.

4. Change your persona to `Developer`.

5. Click `+Add` on the left hand menu, in the centre of the page towards the bottom click `Import from Git`.

6. Paste the following URL in the `Git repo URL` box.
```
https://github.com/mbookham7/pacman-cockroachdb
```
Then click `create`. This will deploy the code from this Git repo. It will fail in the first instance as we have not yet configured our Cloud Database.

## Create Our Cloud Database

7.  Click `+Add` on the left hand menu once again, in the centre of the page at the bottom click `Cloud Hosted Database`.

8. Click `Add to topology`.

9. On the next screen we need to create a new database for our application. Click `Create New Database Instance`.

10. Ensure the correct `Database Provider` and `Provider Account` are selected. Give you Instance a name then click create

11. Click `View Database Instances`, this will display a list of database instances. Select the one you just created and click `Add to Topology`.

12. You will now be taken to the `Topology` screen. Hover over the node.js application, you will see a blue arrow appear. Drag this arrow into the square box surrounding our Database, a `Create Service Binding` label will appear, release the mouse button.

13. A box will appear click `Create`.

## Play Pacman and gain a high score

14. In the Topology view hover over the node.js application once again. Click the icon in the right hand corner of the icon once the `open URL` label appears.

15. A new tab will open with the Pacman game for you to play. Play the game and record a high score.

## Retrieve the High Score from the CockroachDB Cloud Database

16. Login to Cockroach Cloud UI.
```
https://cockroachlabs.cloud/clusters
```

17. Click on `Clusters`. Locate your cluster that you just created and click on the name.

18. In the top right click the button that says `Connect`. To connect the first thing we need to do is download the download the CA Cert and save it in `$HOME/.postgresql/`. To do this expand the section next to `Download CA Cert (Required only once)` by clicking the down arrow. Then click `copy`. Open a terminal window and paste the command there. This will download the certificate file and place it in the correct folder.

19. In the same connect screen change `Select option/language` option to `CockroachDB Client` if you don't already have it installed then download it from here by coping the command from here and pasting it into you terminal window.

20. Below the download section is the `Connect to your database` command. Click on the `Copy` and paste this into your terminal window. This will then ask for a Password.

21. To retrieve the Password switch back to the OpenShift console. Click on the Service Binding which is the arrow between the two boxes. On the right hand side a menu will appear click on the secret.

22. Scroll down to the `data` section and click the copy icon on the right next to the `Password`. Paste this into your terminal window and hit enter.

23. We are now connected to our database instance in Cockroach Cloud. We can now examine the database and check our high score. First lets list the tables in our database.
```
SHOW tables;
```
Example Output:
```
bookham_pacman_proj.bookham_pacman_067890023f@free-tier9.gcp-us-west2.cockroachlabs.cloud:26257/defaultdb> SHOW tables;
  schema_name | table_name | type  |                     owner                     | estimated_row_count | locality
--------------+------------+-------+-----------------------------------------------+---------------------+-----------
  public      | highscores | table | bookham_pacman_proj.bookham_pacman_067890023f |                   0 | NULL
(1 row)


Time: 189ms total (execution 31ms / network 158ms)
```

24. You can see there is one table called `highscores` now we can query this table and see if our high score from the game is present.
```
SELECT * FROM highscores;
```
Example Output:
```
bookham_pacman_proj.bookham_pacman_067890023f@free-tier9.gcp-us-west2.cockroachlabs.cloud:26257/defaultdb> SELECT * FROM highscores;
     name     |  cloud  |  zone   |                host                 | score | level |    date    |  referer  |                                                      user_agent                                                       |                                     hostname                                      |      ip_addr
--------------+---------+---------+-------------------------------------+-------+-------+------------+-----------+-----------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------+--------------------
  MikeBookham | unknown | unknown | pacman-cockroachdb-5bc489d459-cvhn5 |  2540 |     2 | 2022-06-03 | undefined | Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.0.0 Safari/537.36 | pacman-cockroachdb-bookham-pacman-proj.apps.coffeebreak.0x74.p1.openshiftapps.com | ::ffff:10.130.2.8
(1 row)


Time: 199ms total (execution 24ms / network 175ms)
```

25. You should see the high score you created in the game in the `highscores` table in the cloud database! ;-)