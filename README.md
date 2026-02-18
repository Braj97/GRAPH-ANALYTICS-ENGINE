# GRAPH-ANALYTICS-ENGINE
# PLSQL
# CREATE TABLE
CREATE TABLE graph_nodes (
    node_id NUMBER PRIMARY KEY,
    node_name VARCHAR2(100)
);

CREATE TABLE graph_edges (
    edge_id NUMBER PRIMARY KEY,
    from_node NUMBER REFERENCES graph_nodes(node_id),
    to_node NUMBER REFERENCES graph_nodes(node_id),
    weight NUMBER
);
# CREATE SEQUENCE
CREATE SEQUENCE node_seq START WITH 1 INCREMENT BY 1;
CREATE SEQUENCE edge_seq START WITH 1 INCREMENT BY 1;
# CREATE PACKAGE 
CREATE OR REPLACE PACKAGE graph_engine AS

    TYPE node_table IS TABLE OF NUMBER INDEX BY PLS_INTEGER;
    TYPE dist_table IS TABLE OF NUMBER INDEX BY PLS_INTEGER;

    PROCEDURE add_node(p_name VARCHAR2);
    PROCEDURE add_edge(p_from NUMBER, p_to NUMBER, p_weight NUMBER);

    PROCEDURE shortest_path(p_start NUMBER);
    PROCEDURE detect_cycle;
    PROCEDURE display_graph;

END graph_engine;
/
# PACKAGE BODY
CREATE OR REPLACE PACKAGE BODY graph_engine AS

    -- Add Node
    PROCEDURE add_node(p_name VARCHAR2) IS
    BEGIN
        INSERT INTO graph_nodes VALUES (node_seq.NEXTVAL, p_name);
    END;

    -- Add Edge
    PROCEDURE add_edge(p_from NUMBER, p_to NUMBER, p_weight NUMBER) IS
    BEGIN
        INSERT INTO graph_edges 
        VALUES (edge_seq.NEXTVAL, p_from, p_to, p_weight);
    END;

    -- Display Graph
    PROCEDURE display_graph IS
    BEGIN
        DBMS_OUTPUT.PUT_LINE('--- GRAPH NODES ---');
        FOR rec IN (SELECT * FROM graph_nodes) LOOP
            DBMS_OUTPUT.PUT_LINE('Node ID: ' || rec.node_id ||
                                 ' Name: ' || rec.node_name);
        END LOOP;

        DBMS_OUTPUT.PUT_LINE('--- GRAPH EDGES ---');
        FOR rec IN (SELECT * FROM graph_edges) LOOP
            DBMS_OUTPUT.PUT_LINE('Edge: ' || rec.from_node ||
                                 ' -> ' || rec.to_node ||
                                 ' Weight: ' || rec.weight);
        END LOOP;
    END;
# ALGORITHM
PROCEDURE shortest_path(p_start NUMBER) IS
        v_dist dist_table;
        v_visited node_table;
        v_current NUMBER;
        v_min NUMBER;
        v_total_nodes NUMBER;
    BEGIN

        SELECT COUNT(*) INTO v_total_nodes FROM graph_nodes;

        -- Initialize
        FOR i IN 1..v_total_nodes LOOP
            v_dist(i) := 999999;
            v_visited(i) := 0;
        END LOOP;

        v_dist(p_start) := 0;

        FOR i IN 1..v_total_nodes LOOP

            v_min := 999999;

            FOR j IN 1..v_total_nodes LOOP
                IF v_visited(j) = 0 AND v_dist(j) < v_min THEN
                    v_min := v_dist(j);
                    v_current := j;
                END IF;
            END LOOP;

            v_visited(v_current) := 1;

            FOR edge_rec IN (
                SELECT to_node, weight 
                FROM graph_edges 
                WHERE from_node = v_current
            ) LOOP

                IF v_dist(edge_rec.to_node) >
                   v_dist(v_current) + edge_rec.weight THEN

                   v_dist(edge_rec.to_node) :=
                   v_dist(v_current) + edge_rec.weight;

                END IF;

            END LOOP;

        END LOOP;

        DBMS_OUTPUT.PUT_LINE('--- SHORTEST DISTANCES ---');

        FOR i IN 1..v_total_nodes LOOP
            DBMS_OUTPUT.PUT_LINE('Node ' || i ||
                                 ' Distance: ' || v_dist(i));
        END LOOP;

    END;
 # CYCLE DETECTION LOGIC
 PROCEDURE detect_cycle IS
        v_count NUMBER;
    BEGIN
        SELECT COUNT(*) INTO v_count
        FROM graph_edges e1
        WHERE EXISTS (
            SELECT 1
            FROM graph_edges e2
            WHERE e1.from_node = e2.to_node
            AND e1.to_node = e2.from_node
        );

        IF v_count > 0 THEN
            DBMS_OUTPUT.PUT_LINE('Cycle detected in graph.');
        ELSE
            DBMS_OUTPUT.PUT_LINE('No simple cycle detected.');
        END IF;
    END;

END graph_engine;
/
#
