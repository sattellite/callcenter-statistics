Asterisk CallCenter Statistics
---------------

This is two simple scripts that display statistics about callcenter based on Asterisk PBX.

`stats` using `Mojolicious::Lite` and generating simple html table.

`cc_statistics` script must run at cron. It generate data from last hour.

All statistics collecting in special helper table. That sql query will create table:


    CREATE TABLE callcenter_statistics
    (
      id serial NOT NULL,
      date timestamp without time zone NOT NULL DEFAULT now(),
      queue_size_max integer NOT NULL DEFAULT 0,
      queue_size_avg real NOT NULL DEFAULT 0,
      time_in_queue_max integer NOT NULL DEFAULT 0,
      time_in_queue_avg real NOT NULL DEFAULT 0,
      time_with_operator_max integer NOT NULL DEFAULT 0,
      time_with_operator_avg real NOT NULL DEFAULT 0,
      operators integer NOT NULL DEFAULT 0,
      calls integer NOT NULL DEFAULT 0,
      abandoned integer NOT NULL DEFAULT 0,
      abandon_position_avg real NOT NULL DEFAULT 0,
      abandon_timeout_max integer NOT NULL DEFAULT 0,
      abandon_timeout_avg real NOT NULL DEFAULT 0,
      CONSTRAINT callcenter_statistics_pkey PRIMARY KEY (id)
    )
    WITH (
      OIDS=FALSE
    );
    ALTER TABLE callcenter_statistics
      OWNER TO asterisk;
    GRANT ALL ON TABLE callcenter_statistics TO asterisk;
    COMMENT ON TABLE callcenter_statistics
      IS 'CallCenter statistics';
    CREATE INDEX callcenter_statistics_date_idx
      ON callcenter_statistics
      USING btree
      (date);

Add `cc_statistics` to cron.

    1 * * * * /opt/bin/cc_statistics

To start script use:

    morbo stats

And now you can see statistics at http://127.0.0.1:3000