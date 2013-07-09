Asterisk CallCenter Statistics
---------------

This is two simple scripts that display statistics about callcenter based on Asterisk PBX.

`stats` using `Mojolicious::Lite` and generating simple html table.

`cc_statistics` script must run at cron. It generate data from last hour.

All statistics collecting in special helper tables. That sql query will create tables:


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

    CREATE TABLE callcenter_operators
    (
      id serial NOT NULL,
      date timestamp without time zone NOT NULL,
      agent character varying(25) NOT NULL DEFAULT 0,
      answered_calls integer NOT NULL DEFAULT 0,
      notanswered_calls integer NOT NULL DEFAULT 0,
      time_max integer NOT NULL DEFAULT 0,
      time_avg real NOT NULL DEFAULT 0,
      time_sum integer NOT NULL DEFAULT 0,
      CONSTRAINT callcenter_operators_pkey PRIMARY KEY (id),
      CONSTRAINT callcenter_operators_date_agent_key UNIQUE (date, agent)
    )
    WITH (
      OIDS=FALSE
    );
    ALTER TABLE callcenter_operators
      OWNER TO asterisk;
    GRANT ALL ON TABLE callcenter_operators TO asterisk;
    COMMENT ON TABLE callcenter_operators
      IS 'Statistics of agents';
    CREATE INDEX callcenter_operators_agent_id
      ON callcenter_operators
      USING btree
      (agent);
    CREATE INDEX callcenter_operators_agent_id_like
      ON callcenter_operators
      USING btree
      (agent varchar_pattern_ops);
    CREATE INDEX callcenter_operators_date_id
      ON callcenter_operators
      USING btree
      (date);


Add `cc_statistics` to cron.

    1 * * * * /opt/bin/cc_statistics

To start script use:

    morbo stats

And now you can see statistics at http://127.0.0.1:3000