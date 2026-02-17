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
