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
# 
