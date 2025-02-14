Web Smoke Test with Curl and Bash
========================================

Problem
---------

You are creating a CICD pipeline which deploys your web application. After it is deployed you want to be able
to confirm that the application works fine. This verification is sometimes referred as smoke test.

Solution
-------------

You create a bash script which fetches the application status page. To avoid false alarm, you allow the
script to retry multiple times with some delay between the attempts.


.. code-block:: bash

   #! /bin/bash
   export RETRY_INTERVAL=20
   export MAX_RETRY=5
   export TEST_URL="http://localhost:8000/ht/"

   CURL_COMMAND="curl --fail $TEST_URL"

   for i in $(seq $MAX_RETRY); do
      echo "##[command]$CURL_COMMAND"
      $CURL_COMMAND && echo "Success fetching $TEST_URL" && exit 0
      if [ "$i" -lt "$MAX_RETRY" ]
      then
         echo "##[debug]Failed to fetch $TEST_URL. Waiting $RETRY_INTERVAL seconds to retry..."
         sleep $RETRY_INTERVAL
      fi
   done
   echo "##[error]Failed to fetch $TEST_URL after $MAX_RETRY attempts. Giving up."
   exit 1


2023-07-04
