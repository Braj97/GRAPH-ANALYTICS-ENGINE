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
#
